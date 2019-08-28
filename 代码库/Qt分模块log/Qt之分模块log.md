```c++
/********************************************************************************
*
*	Description		log模块，根据传入参数，创建log文件
*	Author			linhaitao
*	Creation Time	2019/8/28
*	Modify
*
*******************************************************************************/

#ifndef LOGGER_H
#define LOGGER_H

#include <qapplication.h>
#include <stdio.h>
#include <stdlib.h>
#include <QFile>
#include <QTextStream>
#include <QMessageBox>
#include <QDir>
#include <QDateTime>
#include <QMutex>

class Logger
{
public:
    Logger(){}
    static void myMessageOutput(QtMsgType type, const QMessageLogContext &context, const QString &msg)
    {
        static QMutex mutex;
        mutex.lock();
        QString log_message;
        QByteArray localMsg = msg.toLocal8Bit();
        switch (type) {
        case QtDebugMsg:
            log_message = QString("Debug\t:");
            fprintf(stderr, "Debug: %s (%s:%u, %s)\n", localMsg.constData(), context.file, context.line, context.function);
            break;
        case QtInfoMsg:
            log_message = QString("Info\t:");
            fprintf(stderr, "Info: %s (%s:%u, %s)\n", localMsg.constData(), context.file, context.line, context.function);
            break;
        case QtWarningMsg:
            log_message = QString("Warning\t:");
            fprintf(stderr, "Warning: %s (%s:%u, %s)\n", localMsg.constData(), context.file, context.line, context.function);
            break;
        case QtCriticalMsg:
            log_message = QString("Critical\t:");
            fprintf(stderr, "Critical: %s (%s:%u, %s)\n", localMsg.constData(), context.file, context.line, context.function);
            break;
        case QtFatalMsg:
            fprintf(stderr, "Fatal: %s (%s:%u, %s)\n", localMsg.constData(), context.file, context.line, context.function);
            abort();
        }
        fflush(stderr);//立即输出，msvc没有这句也会立即输出，但是mingw不会


#ifndef QT_DEBUG
        if(QtDebugMsg == type)
        {
            mutex.unlock();
            return;
        }
        log_message = QString("%1 %2").arg(log_message).arg(msg);
#else	//release下不会有文件以及函数信息
        log_message = QString("%1 %2\t(%3:%4, %5)").arg(log_message).arg(msg).arg(context.file).arg(context.line).arg(context.function);
#endif // else

        QDir dir;
        if(!dir.exists(logPath))
        {
            dir.mkpath(logPath);
        }

        QDateTime current_time = QDateTime::currentDateTime();
        QString log_file_name = QString("%1/%2_%3.log").arg(logPath)
                .arg(context.category)
                .arg(QDate::currentDate().toString("yyyyMMdd"));

        QFile outFile(log_file_name);
        if (outFile.open(QIODevice::WriteOnly | QIODevice::Append | QIODevice::Text))
        {
            QTextStream write_log(&outFile);
            write_log << current_time.toString("yyyy-MM-dd hh:mm:ss") << "-";
            write_log << log_message << endl;
            outFile.close();
        }

        mutex.unlock();
    }

private:
    static QString logPath;
};

//默认当前路径下的log文件夹下
QString Logger::logPath = QString("%1/log").arg(QDir::currentPath());

#endif
```
使用时，在项目目录下新建 ***QDebug*** 文件，里面重新定义qDebug等宏定义，其他不变
```c++
#include "qdebug.h"
#undef qDebug
#define qDebug(...) QMessageLogger(QT_MESSAGELOG_FILE, QT_MESSAGELOG_LINE, QT_MESSAGELOG_FUNC, ##__VA_ARGS__).debug()

#undef qInfo
#define qInfo(...) QMessageLogger(QT_MESSAGELOG_FILE, QT_MESSAGELOG_LINE, QT_MESSAGELOG_FUNC, ##__VA_ARGS__).info()

#undef qWarning
#define qWarning(...) QMessageLogger(QT_MESSAGELOG_FILE, QT_MESSAGELOG_LINE, QT_MESSAGELOG_FUNC, ##__VA_ARGS__).warning()

#undef qCritical
#define qCritical(...) QMessageLogger(QT_MESSAGELOG_FILE, QT_MESSAGELOG_LINE, QT_MESSAGELOG_FUNC, ##__VA_ARGS__).critical()
```



