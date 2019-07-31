**可用于发送数据**

```c++
#ifndef DATAWRITER
#define DATAWRITER

#include <QString>

class DataWriter
{
public:

    static int writeInt(QByteArray& block, int offset, int value)
    {
        block[offset++] = (unsigned char)(value >> 24 & 0xff);
        block[offset++] = (unsigned char)(value >> 16 & 0xff);
        block[offset++] = (unsigned char)(value >> 8 & 0xff);
        block[offset++] = (unsigned char)(value & 0xff);
        return offset;
    }

    static int writeShort(QByteArray& block, int offset, short value)
    {
        block[offset++] = (unsigned char)(value >> 8 & 0xff);
        block[offset++] = (unsigned char)(value & 0xff);
        return offset;
    }

    static int writeFloat(QByteArray &block, int offset, float value)
    {
        int *dd = (int *)&value;
        block[offset++] = (unsigned char)((*dd) & 0xff);
        block[offset++] = (unsigned char)((*dd) >> 8 & 0xff);
        block[offset++] = (unsigned char)((*dd) >> 16 & 0xff);
        block[offset++] = (unsigned char)((*dd) >> 24 & 0xff);
        return offset;
    }

    static int readInt(QByteArray data, int dpos)
    {
        int i1 = data[dpos++] & 0xff;
        int i2 = data[dpos++] & 0xff;
        int i3 = data[dpos++] & 0xff;
        int i4 = data[dpos++] & 0xff;
        int val1 = i4 + ((i3 & 0xff) << 8) + ((i2 & 0xff) << 16) + ((i1 & 0xff) << 24);
        return val1;
    }

    static short readShort(QByteArray data, int dpos)
    {
        int i1 = data[dpos++] & 0xff;
        int i2 = data[dpos++] & 0xff;

        short val1 = i2 + ((i1 & 0xff) << 8);
        return val1;
    }

    static int writeChar(QByteArray &dest, int offset, char value)
    {
        dest[offset++] = value;

        return offset;
    }

    static int writeByteArray(QByteArray &dest, int offset, QByteArray source)
    {
        dest.append(source);

        offset += source.size();

        return offset;
    }

    static int writeString(QByteArray &data, int offset, QString t, int len)
    {
        QString text = t;

        for (int i = 0; i < len; i++)
        {
            if (text.length() <= i)
            {
                data[offset++] = 0x20;
            }
            else
            {
                data[offset++] = text.at(i).toLatin1();
            }
        }

        return offset;
    }

    static int writeChineseString(QByteArray &data, int offset, QString t, int len)
    {
        QByteArray baTmp;
        baTmp = t.toUtf8();

        for (int i = 0; i < len; i++)
        {
            if (baTmp.length() <= i)
            {
                data[offset++] = 0x20;
            }
            else
            {
                data[offset++] = baTmp.at(i);
            }
        }

        return offset;
    }
};

#endif // DATAWRITER
```