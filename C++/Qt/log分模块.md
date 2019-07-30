## 说明

对于一般的log，使用 `qInstallMessageHandler` 重定向到文件即可，甚至可以根据日志等级，分类存储。但是并不是适用所有情况，比如，程序运行时动态创建模块，而每个模块需要创建不用的日志记录。参考Qt的 `QDebug` 代码，重新编写日志模块。

## 代码

**思路如下：**

- 重新定义`qDebug`等宏，尽可能保证原来的log风格不变；
- 重载运算符 `<<`，进行代码导向；
- 最后写文件，操作，注意线程安全；
- 使用前，现将模块名和log的存放地址注册绑定，使用时，根据模块名即可知道对应模块；

```c++
//logger.h
#ifndef LOGGER_H
#define LOGGER_H

#include <QString>
#include <QHash>
#include <QMutex>

#undef qDebug
#undef qWarning
#undef qInfo
#undef qCritical
#undef qFatal

#define qDebug Logger(QT_MESSAGELOG_FILE, QT_MESSAGELOG_LINE, QT_MESSAGELOG_FUNC).debug
#define qWarning Logger(QT_MESSAGELOG_FILE, QT_MESSAGELOG_LINE, QT_MESSAGELOG_FUNC).warning
#define qInfo Logger(QT_MESSAGELOG_FILE, QT_MESSAGELOG_LINE, QT_MESSAGELOG_FUNC).info
#define qCritical Logger(QT_MESSAGELOG_FILE, QT_MESSAGELOG_LINE, QT_MESSAGELOG_FUNC).critical
#define qFatal Logger(QT_MESSAGELOG_FILE, QT_MESSAGELOG_LINE, QT_MESSAGELOG_FUNC).fatal

class DebugLogger{
public:
    DebugLogger(){ }
    void operator<<(const QString& message);
};

class InfoLogger{
public:
    InfoLogger(){ }
    void operator<<(const QString& message);
};

class WarningLogger{
public:
    WarningLogger(){ }
    void operator<<(const QString& message);
};

class CriticalLog{
public:
    CriticalLog(){ }
    void operator<<(const QString& message);
};

class FatalLog{
public:
    FatalLog(){ }
    void operator<<(const QString& message);
};

class Logger{
public:
    Logger(const char *codeFileName, int line, const char *function);

    static void Initlize(const QString& module, const QString& file_name);

    static void WriteLog(QtMsgType type, const QString& message);

    inline DebugLogger debug() const { return debugLogger; }
    inline DebugLogger debug(const QString& module){
        logFileName = fileNameHash.value(module);
        return debugLogger;
    }

    inline InfoLogger info() const { return infoLogger; }
    inline InfoLogger info(const QString& module){
        logFileName = fileNameHash.value(module);
        return infoLogger;
    }

    inline WarningLogger warning() const { return warningLogger; }
    inline WarningLogger warning(const QString& module){
        logFileName = fileNameHash.value(module);
        return warningLogger;
    }

    inline CriticalLog critical() const { return criticalLog; }
    inline CriticalLog critical(const QString& module){
        logFileName = fileNameHash.value(module);
        return criticalLog;
    }

    inline FatalLog fatal() const { return fatalLog; }
    inline FatalLog fatal(const QString& module){
        logFileName = fileNameHash.value(module);
        return fatalLog;
    }

private:
    static QHash<QString, QString> fileNameHash;
    static QString codeFileName;
    static int codeLine;
    static QString codeFunction;

    static QString logFileName;
    static QMutex mutex;

    DebugLogger debugLogger;
    InfoLogger infoLogger;
    WarningLogger warningLogger;
    CriticalLog criticalLog;
    FatalLog fatalLog;
};

#endif // LOGGER_H
```

```c++
//logger.cpp
#include "logger.h"
#include <QApplication>
#include <QFile>
#include <QFileInfo>
#include <QDir>
#include <QDateTime>
#include <QTextStream>

QHash<QString, QString> Logger::fileNameHash;
QString Logger::codeFileName;
int Logger::codeLine = 0;
QString Logger::codeFunction;
QString Logger::logFileName;
QMutex Logger::mutex;

Logger::Logger(const char *file, int line, const char *function)
{
    codeFileName = file;
    codeLine = line;
    codeFunction = function;
}

void Logger::Initlize(const QString &module, const QString &file_name)
{
    QFileInfo file_info(file_name);

    QDir dir = file_info.absoluteDir();
    if(!dir.exists())
    {
        dir.mkpath(dir.path());
    }
    fileNameHash.insert(module, file_name);
}

void Logger::WriteLog(QtMsgType type, const QString &message)
{
    if(logFileName.isEmpty())
    {
        return;
    }
    mutex.lock();

    QString log_message;
    QByteArray localMsg = message.toLocal8Bit();
    const char* code_file_name = codeFileName.toLocal8Bit().constData();
    const char* code_function = codeFunction.toLocal8Bit().constData();
    switch (type)
    {
    case QtDebugMsg:
        log_message = QString("Debug\t:");
        fprintf(stderr, "Debug: %s (%s:%u, %s)\n", localMsg.constData(), code_file_name, codeLine, code_function);
        break;
    case QtInfoMsg:
        log_message = QString("Infom\t:");
        fprintf(stderr, "Info: %s (%s:%u, %s)\n", localMsg.constData(), code_file_name, codeLine, code_function);
        break;
    case QtWarningMsg:
        log_message = QString("Warning\t:");
        fprintf(stderr, "Warning: %s (%s:%u, %s)\n", localMsg.constData(), code_file_name, codeLine, code_function);
        break;
    case QtCriticalMsg:
        log_message = QString("Critical\t:");
        fprintf(stderr, "Critical: %s (%s:%u, %s)\n", localMsg.constData(), code_file_name, codeLine, code_function);
        break;
    case QtFatalMsg:
        fprintf(stderr, "Fatal: %s (%s:%u, %s)\n", localMsg.constData(), code_file_name, codeLine, code_function);
        //Q_ASSERT_X(true, code_file_name, "Fatal error");
        mutex.unlock();
        abort();
    }
    fflush(stderr);//立即输出，msvc没有这句也会立即输出，但是mingw不会

#ifndef QT_DEBUG
    if(QtDebugMsg == type)
    {
        mutex.unlock();
        return;
    }
    log_message = QString("%1 %2").arg(log_message).arg(message);
#else	//release下不会有文件以及函数信息
    log_message = QString("%1 %2\t(%3:%4, %5)").arg(log_message).arg(message).arg(codeFileName).arg(codeLine).arg(codeFunction);
#endif // else

    QFile outFile(logFileName);
    if (outFile.open(QIODevice::WriteOnly | QIODevice::Append | QIODevice::Text))
    {
        QTextStream write_log(&outFile);
        write_log << QDateTime::currentDateTime().toString("yyyy-MM-dd hh:mm:ss") << "-";
        write_log << log_message << endl;
        outFile.close();
    }

    mutex.unlock();
}

void DebugLogger::operator<<(const QString &message)
{
    Logger::WriteLog(QtDebugMsg, message);
}

void WarningLogger::operator<<(const QString &message)
{
    Logger::WriteLog(QtWarningMsg, message);
}

void InfoLogger::operator<<(const QString &message)
{
    Logger::WriteLog(QtInfoMsg, message);
}

void CriticalLog::operator<<(const QString &message)
{
    Logger::WriteLog(QtCriticalMsg, message);
}

void FatalLog::operator<<(const QString &message)
{
    Logger::WriteLog(QtFatalMsg, message);
}
```

```c++
void main()
{
    //注册模块名和log存放地址
    Logger::Initlize("test", "test.log");
    Logger::Initlize("test2", "test2.log");

    //根据模块名，存放不同的log
    qDebug("test2")<<"111";
    qDebug("test")<<"123";
    qWarning("test2")<<"111";
    qWarning("test")<<"123";
    qInfo("test")<<"中文";
    qInfo("test2")<<"中文";
    qCritical("test")<<"中文1";
    qCritical("test2")<<"中文1";
}
```





