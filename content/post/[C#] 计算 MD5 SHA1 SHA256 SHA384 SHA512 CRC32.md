---
title: '[C#] 计算 MD5 SHA1 SHA256 SHA384 SHA512 CRC32'
date: 2021-04-18 02:38:55
tags:
  - c#
  - .net
  - 字符串
categories:
  - 类库
  - .NET
  - 灌水
description: '直接贴代码了, 复制即可用, 源码部分来自网络.如果要计算字符串的 MD5 值, 直接 Encoding.UTF8.GetBytes() 然后就可以了using System;using System.IO;using System.Linq;namespace NullLib.HashCalc{    public class HashHelper    {        public static string CalcMd5x32(byte[] bytValue)        '
---

直接贴代码了, 复制即可用, 源码部分来自网络.


如果要计算字符串的 MD5 值, 直接 Encoding.UTF8.GetBytes() 然后就可以了

```csharp
using System;
using System.IO;
using System.Linq;

namespace NullLib.HashCalc
{
    public class HashHelper
    {
        public static string CalcMd5x32(byte[] bytValue)
        {
            return CalcMd5x32(new MemoryStream(bytValue));
        }
        public static string CalcMd5x16(byte[] bytValue)
        {
            return CalcMd5x16(new MemoryStream(bytValue));
        }
        public static string CalcShax1(byte[] bytValue)
        {
            return CalcShax1(new MemoryStream(bytValue));
        }
        public static string CalcShax256(byte[] bytValue)
        {
            return CalcShax256(new MemoryStream(bytValue));
        }
        public static string CalcShax384(byte[] bytValue)
        {
            return CalcShax384(new MemoryStream(bytValue));
        }
        public static string CalcShax512(byte[] bytValue)
        {
            return CalcShax512(new MemoryStream(bytValue));
        }

        public static string CalcMd5x32(Stream stream)
        {
            var MD5CSP = new System.Security.Cryptography.MD5CryptoServiceProvider();

            byte[] bytHash = MD5CSP.ComputeHash(stream);
            MD5CSP.Clear();

            string sHash = BytesToHex(bytHash);
            return sHash;
        }
        public static string CalcMd5x16(Stream stream)
        {
            return CalcMd5x32(stream).Substring(8, 16);
        }
        public static string CalcShax1(Stream stream)
        {
            var SHA1CSP = new System.Security.Cryptography.SHA1CryptoServiceProvider();

            byte[] bytHash = SHA1CSP.ComputeHash(stream);
            SHA1CSP.Clear();

            string sHash = BytesToHex(bytHash);
            return sHash;
        }
        public static string CalcShax256(Stream stream)
        {
            var SHA256CSP = new System.Security.Cryptography.SHA256CryptoServiceProvider();

            byte[] bytHash = SHA256CSP.ComputeHash(stream);
            SHA256CSP.Clear();

            string sHash = BytesToHex(bytHash);
            return sHash;
        }
        public static string CalcShax384(Stream stream)
        {
            var SHA384CSP = new System.Security.Cryptography.SHA384CryptoServiceProvider();

            byte[] bytHash = SHA384CSP.ComputeHash(stream);
            SHA384CSP.Clear();

            string sHash = BytesToHex(bytHash);
            return sHash;
        }
        public static string CalcShax512(Stream stream)
        {
            var SHA512CSP = new System.Security.Cryptography.SHA512CryptoServiceProvider();

            byte[] bytHash = SHA512CSP.ComputeHash(stream);
            SHA512CSP.Clear();

            string sHash = BytesToHex(bytHash);
            return sHash;
        }

        public static string CalcCrcx32(Stream stream)
        {
            Crc32 calculator = new Crc32();
            byte[] buffer = calculator.ComputeHash(stream);
            calculator.Clear();
            //将字节数组转换成十六进制的字符串形式
            string sHash = BytesToHex(buffer);
            return sHash;
        }

        private static string BytesToHex(byte[] array)
        {
            return string.Join(null, array.Select(v => Convert.ToString(v, 16).PadLeft(2, '0')));
        }

        /// <summary>
        /// 提供 CRC32 算法的实现
        /// </summary>
        private class Crc32 : System.Security.Cryptography.HashAlgorithm
        {
            public const uint DefaultPolynomial = 0xedb88320;
            public const uint DefaultSeed = 0xffffffff;
            private uint hash;
            private uint seed;
            private uint[] table;
            private static uint[] defaultTable;
            public Crc32()
            {
                table = InitializeTable(DefaultPolynomial);
                seed = DefaultSeed;
                Initialize();
            }
            public Crc32(uint polynomial, uint seed)
            {
                table = InitializeTable(polynomial);
                this.seed = seed;
                Initialize();
            }
            public override void Initialize()
            {
                hash = seed;
            }
            protected override void HashCore(byte[] buffer, int start, int length)
            {
                hash = CalculateHash(table, hash, buffer, start, length);
            }
            protected override byte[] HashFinal()
            {
                byte[] hashBuffer = uintToBigEndianBytes(~hash);
                this.HashValue = hashBuffer;
                return hashBuffer;
            }
            public static uint Compute(byte[] buffer)
            {
                return ~CalculateHash(InitializeTable(DefaultPolynomial), DefaultSeed, buffer, 0, buffer.Length);
            }
            public static uint Compute(uint seed, byte[] buffer)
            {
                return ~CalculateHash(InitializeTable(DefaultPolynomial), seed, buffer, 0, buffer.Length);
            }
            public static uint Compute(uint polynomial, uint seed, byte[] buffer)
            {
                return ~CalculateHash(InitializeTable(polynomial), seed, buffer, 0, buffer.Length);
            }
            private static uint[] InitializeTable(uint polynomial)
            {
                if (polynomial == DefaultPolynomial && defaultTable != null)
                {
                    return defaultTable;
                }
                uint[] createTable = new uint[256];
                for (int i = 0; i < 256; i++)
                {
                    uint entry = (uint)i;
                    for (int j = 0; j < 8; j++)
                    {
                        if ((entry & 1) == 1)
                            entry = (entry >> 1) ^ polynomial;
                        else
                            entry = entry >> 1;
                    }
                    createTable[i] = entry;
                }
                if (polynomial == DefaultPolynomial)
                {
                    defaultTable = createTable;
                }
                return createTable;
            }
            private static uint CalculateHash(uint[] table, uint seed, byte[] buffer, int start, int size)
            {
                uint crc = seed;
                for (int i = start; i < size; i++)
                {
                    unchecked
                    {
                        crc = (crc >> 8) ^ table[buffer[i] ^ crc & 0xff];
                    }
                }
                return crc;
            }
            private byte[] uintToBigEndianBytes(uint x)
            {
                return new byte[] { (byte)((x >> 24) & 0xff), (byte)((x >> 16) & 0xff), (byte)((x >> 8) & 0xff), (byte)(x & 0xff) };
            }
        }
    }
}
```
