---
title: '斗鱼弹幕获取程序C#.NET CORE版本'
date: 2016-09-19 01:26:21
categories: 编程
tags: [.net core, c#, 斗鱼, 弹幕]
urlname: crawl_the_douyu_danmu_by_c_sharp_program
---

## 文章更新

1. 20160919-初次成文

## 为什么会有这篇文章

之前有一阵子对斗鱼的弹幕比较感兴趣，就参考网上其他大神的办法，用C#实现了一个控制台程序，很简陋，但是效果很不错，有需要的可以拿去扩展功能。我最希望的就是实现登录功能，比如和用户的反馈，程序可以自动根据用户发送的消息进行反馈，比如输入 签到，程序就会说一句‘xxx签到成功，金钱增加10’这样的。不过这样看来，和QQ群内的程序也是一个思路。<!-- more -->

## 直接上代码

之前是基于.net 4.5写的，因为最近在学习.net core，所以就改写了一下，这样在pc或者mac下都可以用了，甚至在linux也可以做成一个服务让它24小时的跑。

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Security.Cryptography;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    public class Program
    {
        public static string strDanmuUrl = "danmu.douyutv.com";
        public static int intPort = 8602;
        public static int intRoomID = 105025;
        public DateTime dtStart = DateTime.Now.ToLocalTime();
        static Socket newClient = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);

        public static void Main(string[] args)
        {
            byte[] byteData = new byte[1024];
            var strIpAddress = GetIPbyNameAsync(strDanmuUrl);
            var ie = new IPEndPoint(IPAddress.Parse(strIpAddress.Result), intPort);
            try
            {
                newClient.Connect(ie);
            }
            catch (SocketException e1)
            {
                Console.WriteLine("Can not connect to Internet, the Error is {0}.", e1.ToString());
                return;
            }

            string strMsg2Send = string.Format("type@=loginreq/username@={0}/password@={1}/roomid@={2}/", "visitor5585580", "1234567890123456", intRoomID);
            //把字符串转变为byte array.
            byte[] byteSend = ProcessString(strMsg2Send);
            //把登录字符串(bytes)发送给服务器.
            newClient.Send(byteSend, byteSend.Length, 0);
            //从服务器接收到的bytes.
            byte[] byteRecvive = new byte[1024];
            newClient.Receive(byteRecvive, byteRecvive.Length, 0);
            //将byte array转变成string.
            string strReceive = Encoding.UTF8.GetString(byteRecvive, 0, byteRecvive.Length);
            Console.OutputEncoding = Encoding.UTF8;
            Console.WriteLine("The message received from Douyu service is {0}", strReceive);
            Console.WriteLine();

            int intGid = GetGid();

            string strSend2 = string.Format("type@=joingroup/rid@={0}/gid@={1}/", intRoomID, intGid);

            byte[] byteSend2 = ProcessString(strSend2);

            newClient.Send(byteSend2, byteSend2.Length, 0);

            Thread keepLiveThread = new Thread(new ThreadStart(KeepLiveThread));

            keepLiveThread.IsBackground = true;
            keepLiveThread.Start();

            DateTime dtBegin = DateTime.Now.ToLocalTime();

            while (true)
            {
                byte[] byteReceive2 = new byte[1024];
                newClient.Receive(byteReceive2, byteReceive2.Length, 0);
                string strReceivce2 = Encoding.UTF8.GetString(byteReceive2, 12, byteReceive2.Length - 12);
                string strDanmu = ProcessAndShowDanmu(strReceivce2);

                LogAppend(strDanmu);
            }
        }

        private static void LogAppend(string strDanmu)
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("\n");
            Console.WriteLine(strDanmu);
            Console.WriteLine("\n");
        }

        private static string ProcessAndShowDanmu(string strRecv)
        {
            if (strRecv.IndexOf("type@=") == -1)
            {
                Console.WriteLine(strRecv);
                Console.WriteLine("无效信息");
            }
            else if (strRecv.IndexOf("type@=error") > 0)
            {
                Console.WriteLine(strRecv);
                Console.WriteLine("错误信息，可能认证失败");
            }
            else
            {
                strRecv = strRecv.Replace("@S", "/").Replace("@A=", ":").Replace("@=", ":");
                Console.WriteLine(strRecv);
                string msg_type_zh = "";
                try
                {
                    Match m = Regex.Match(strRecv, "type:(.+?)\\/");
                    if (m.Groups[1].Value == "chatmessage")
                    {
                        msg_type_zh = "弹幕消息";
                        Match m2 = Regex.Match(strRecv, "\\/sender:(.+?)\\/");
                        string sender_id = m2.Groups[1].Value;
                        Match m3 = Regex.Match(strRecv, "\\/snick:(.+?)\\/");
                        string nickname = m3.Groups[1].Value;
                        Match m4 = Regex.Match(strRecv, "\\/content:(.+?)\\/");
                        string content = m4.Groups[1].Value;
                        Match m5 = Regex.Match(strRecv, "\\/strength:(.+?)\\/");
                        string strength = m5.Groups[1].Value;
                        Match m6 = Regex.Match(strRecv, "\\/level:(.+?)\\/");
                        string level = m6.Groups[1].Value;
                        DateTime dt = DateTime.Now;
                        string datetime = dt.ToString("yyyy-MM-dd HH:mm:ss");
                        String strContent = "|" + msg_type_zh + "| " + align_left_str(nickname, 20, ' ') +
                                          align_left_str("<Lv:" + level + ">", 8, ' ') +
                                          align_left_str("(" + sender_id + ")", 13, ' ') +
                                          align_left_str("[" + strength + "]", 10, ' ') + "@ " + datetime + ": " +
                                          content + ' ';
                        return strContent;
                    }
                    else if (m.Groups[1].Value == "userenter")
                    {
                        msg_type_zh = "入房消息";
                        Match m2 = Regex.Match(strRecv, "\\/userinfo:id:(.+?)\\/");
                        string user_id = m2.Groups[1].Value;
                        Match m3 = Regex.Match(strRecv, "\\/nick:(.+?)\\/");
                        string nickname = m3.Groups[1].Value;
                        Match m4 = Regex.Match(strRecv, "\\/strength:(.+?)\\/");
                        string strength = m4.Groups[1].Value;
                        Match m5 = Regex.Match(strRecv, "\\/level:(.+?)\\/");
                        string level = m5.Groups[1].Value;
                        DateTime dt = DateTime.Now;
                        string datetime = dt.ToString("yyyy-MM-dd HH:mm:ss");
                        String strContent = "|" + msg_type_zh + "| " + align_left_str(nickname, 20, ' ') +
                                          align_left_str("<Lv:" + level + ">", 8, ' ') +
                                          align_left_str("(" + user_id + ")", 13, ' ') +
                                          align_left_str("[" + strength + "]", 10, ' ') + "@ " + datetime;
                        return strContent;
                    }
                    else if (m.Groups[1].Value == "dgn")
                    {
                        msg_type_zh = "鱼丸赠送";
                        Match m2 = Regex.Match(strRecv, "\\/level:(\\d+?)\\/");
                        string level = m2.Groups[1].Value;
                        Match m3 = Regex.Match(strRecv, "\\/sid:(.+?)\\/");
                        string user_id = m3.Groups[1].Value;
                        Match m4 = Regex.Match(strRecv, "\\/src_ncnm:(.+?)\\/");
                        string nickname = m4.Groups[1].Value;
                        Match m5 = Regex.Match(strRecv, "\\/hits:(.+?)\\/");
                        string hits = m5.Groups[1].Value;
                        DateTime dt = DateTime.Now;
                        string datetime = dt.ToString("yyyy-MM-dd HH:mm:ss");
                        String strContent = "|" + msg_type_zh + "| " + align_left_str(nickname, 20, ' ') +
                                          align_left_str("<Lv:" + level + ">", 8, ' ') +
                                          align_left_str("(" + user_id + ")", 13, ' ') +
                                          align_left_str("[unknown]", 10, ' ') + "@ " + datetime + ": " + hits +
                                          " hits ";
                        return strContent;
                    }
                    else
                    {
                        msg_type_zh = "未知类型";
                        String strContent = "|" + msg_type_zh + "| " + strRecv;
                        return strContent;
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.ToString());
                }
                //finally
                //{
                //    Console.WriteLine("|该句异常| " + strRecv);
                //}
            }
            return "空";
        }

        private static string align_left_str(string raw_str, int max_length, char filled_chr)
        {
            int my_length = 0;
            foreach (char c in raw_str)
            {
                if (Convert.ToInt32(c) > 127 || Convert.ToInt32(c) < 0)
                {
                    my_length++;
                }
            }
            if ((max_length - my_length) > 0)
            {
                return raw_str + filled_chr * (max_length - my_length);
            }
            else
            {
                return raw_str;
            }
        }

        private static void KeepLiveThread()
        {
            while (true)
            {
                Thread.Sleep(40000);
                TimeSpan ts = DateTime.UtcNow - new DateTime(1970, 1, 1, 0, 0, 0, 0);
                //string strKeepAlive = string.Format("type@=mrkl/");
                string strKeepAlive = string.Format("type@=keeplive/tick@={0}/", Convert.ToInt64(ts.TotalSeconds));
                byte[] bytesSend = ProcessString(strKeepAlive);
                newClient.Send(bytesSend, bytesSend.Length, 0);
            }
        }

        private static int GetGid()
        {
            string ipadd2 = "119.90.49.107";
            var port2 = 8034;
            var newclient2 = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
            var ie2 = new IPEndPoint(IPAddress.Parse(ipadd2), port2);
            newclient2.Connect(ie2);

            string devid = System.Guid.NewGuid().ToString().Replace("-", "").ToUpper();
            string rt = (DateTimeHelperClass.CurrentUnixTimeMillis() / 1000).ToString();

            string ver = "20150526";
            string magic = "7oE9nPEG9xXV69phU31FYCLUagKeYtsF";

            byte[] result = Encoding.UTF8.GetBytes(rt + magic + devid);
            //MD5 md5 = new MD5CryptoServiceProvider()
            string tmpoutput;
            using (var md5 = MD5.Create())
            {
                var result2 = md5.ComputeHash(result);
                tmpoutput = BitConverter.ToString(result2).Replace("-", "");
            }
            string vk = tmpoutput;

            string strGetGid = string.Format("type@=loginreq/username@=/ct@=2/password@=/roomid@=" + intRoomID +
                    "/devid@=" + devid + "/rt@=" + rt + "/vk@=" + vk + "/ver@=" + ver + "/");

            byte[] bytesSend = ProcessString(strGetGid);
            newclient2.Send(bytesSend, bytesSend.Length, 0);
            byte[] data = new byte[1024];
            //下面这个地方有点tricky，从wireshark里发现的，第一条返回数据没啥用，第二条里面才有gid数据
            var bytesRecv3 = newclient2.Receive(data, data.Length, 0);
            var bytesRecv3_2 = newclient2.Receive(data, data.Length, 0);
            string strRecv = Encoding.UTF8.GetString(data, 0, bytesRecv3_2);
            Console.WriteLine("接受到的数据" + strRecv);
            int gid = getValueByName(strRecv);
            Console.WriteLine("从服务器({0})断开连接", ipadd2);

            byte[] bytesSend2 = loginReq("", "", "510792");
            newclient2.Send(bytesSend2, bytesSend2.Length, 0);
            newclient2.Shutdown(SocketShutdown.Both);
            newclient2.Dispose();
            return gid;
        }

        private static byte[] loginReq(string username, string password, string roomid)
        {
            string uuid = System.Guid.NewGuid().ToString().Replace("-", "").ToUpper();

            TimeSpan ts = DateTime.UtcNow - new DateTime(1970, 1, 1, 0, 0, 0, 0);
            long time = Convert.ToInt64(ts.TotalSeconds);
            string salt = "7oE9nPEG9xXV69phU31FYCLUagKeYtsF";

            string vkTmp = string.Format("{0}{1}{2}", time, salt, uuid);
            byte[] result = Encoding.UTF8.GetBytes(vkTmp);
            string tmpoutput;
            using (var md5 = MD5.Create())
            {
                var result2 = md5.ComputeHash(result);
                tmpoutput = BitConverter.ToString(result2).Replace("-", "");
            }
            string vk = tmpoutput;
            string p = string.Format("type@=loginreq/username@={0}/password@={1}/roomid@={2}/ct@=2/devid@={3}/ver@={4}/rt@={5}/vk@={6}/",
                username, password, roomid, uuid, 20150515, time, vk);
            byte[] bteAry0 = Encoding.UTF8.GetBytes(p);
            byte[] bteAry1 = Encoding.UTF8.GetBytes("dc000000 dc000000 b1020000 ");
            byte[] buf = new byte[bteAry0.Length + bteAry1.Length];

            bteAry0.CopyTo(buf, 0);
            bteAry1.CopyTo(buf, bteAry0.Length); //后面第二个参数，是里面已经存在的byte[]的长度
            //buf.Put(0);
            return buf;
        }

        private static int getValueByName(string strRecv)
        {
            int gid = 0;
            if (strRecv.IndexOf("type@=") == -1)
            {
                Console.WriteLine(strRecv);
                Console.WriteLine("无效信息");
            }
            else if (strRecv.IndexOf("type@=error") > 0)
            {
                Console.WriteLine(strRecv);
                Console.WriteLine("错误信息，可能认证失败");
            }
            else if (strRecv.IndexOf("type@=mrkl") > 0)
            {
                Console.WriteLine(strRecv);
                Console.WriteLine("服务器接收到了心跳数据");
            }
            else
            {
                strRecv = strRecv.Replace("@S", "/").Replace("@A=", ":").Replace("@=", ":");
                try
                {
                    Match m2 = Regex.Match(strRecv, "\\/gid:(.+?)\\/");
                    gid = Convert.ToInt16(m2.Groups[1].Value.ToString());
                    Console.WriteLine(gid);
                }
                catch
                {

                }
                return gid;
            }
            return 0;
        }

        private static byte[] ProcessString(string strMsg2Send)
        {
            string msgToPro = new string(new char[1024]);
            msgToPro = strMsg2Send;
            int i = msgToPro.Length + 1 + 8;
            byte[] bytes1 = BitConverter.GetBytes(i);
            byte[] bs = Encoding.ASCII.GetBytes(msgToPro);
            byte[] bytes2 = new byte[] { 0xb1, 0x02, 0x00, 0x00 };
            byte[] bytes3 = new byte[] { 0x00 };
            byte[] resArr = new byte[bytes1.Length + bytes1.Length + bytes2.Length + bs.Length + bytes3.Length];
            bytes1.CopyTo(resArr, 0);
            bytes1.CopyTo(resArr, bytes1.Length);
            bytes2.CopyTo(resArr, bytes1.Length + bytes1.Length);
            bs.CopyTo(resArr, bytes1.Length + bytes1.Length + bytes2.Length);
            bytes3.CopyTo(resArr, bytes1.Length + bytes1.Length + bytes2.Length + bs.Length);
            return resArr;
        }

        private static async Task<string> GetIPbyNameAsync(string s)
        {
            IPAddress[] host = await Dns.GetHostAddressesAsync(s);
            return await Task.FromResult(host[0].ToString());
        }
    }

    internal static class DateTimeHelperClass
    {
        private static readonly System.DateTime Jan1st1970 = new System.DateTime(1970, 1, 1, 0, 0, 0, System.DateTimeKind.Utc);
        internal static long CurrentUnixTimeMillis()
        {
            return (long)(System.DateTime.UtcNow - Jan1st1970).TotalMilliseconds;
        }
    }
}
```

没啥多说的，有空继续改进吧，最近在想怎么获取QQ群内的聊天，并且保存起来晚上有空看看大家聊了什么有营养的话题，当然了，感觉基本上都是老司机在扯淡，不过净化还是有的。

## 参考文章

1. []()
2. []()

