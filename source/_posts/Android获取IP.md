---
title: Android获取IP
date: 2017-09-25 20:11:03
tags: Android
categories: Android
---

# 0x00 前言

学过计算机网络的都知道，计算机IP地址分为内网IP和外网IP。

在Android中API只提供了获取内网IP的方法，那如何获取外网IP呢？要解决这个问题要借助于服务器或者提供该服务的网站了。

# 0x01 内网IP

Android系统提供了获取内网IP的API，下面看如何使用API实现：

```java
    /**
     * 获取本地ip地址
     *
     * @return
     */
    public static String getHostIP() {
        String hostIp = null;
        try {
            Enumeration nis = NetworkInterface.getNetworkInterfaces();
            InetAddress ia = null;
            while (nis.hasMoreElements()) {
                NetworkInterface ni = (NetworkInterface) nis.nextElement();
                Enumeration<InetAddress> ias = ni.getInetAddresses();
                while (ias.hasMoreElements()) {
                    ia = ias.nextElement();
                    if (ia instanceof Inet6Address) {
                        continue;// skip ipv6
                    }
                    String ip = ia.getHostAddress();
                    if (!"127.0.0.1".equals(ip)) {
                        hostIp = ia.getHostAddress();
                        break;
                    }
                }
            }
        } catch (SocketException e) {
            e.printStackTrace();
        }
        return hostIp;

    }
```

<!--more-->



# 0x02 外网IP

由于Android系统没有提供获取外网IP的API，所以只能通过间接的方法获取。了解Http原理的都知道，客户端向服务器发送请求时会携带MAC和IP地址，而这里的IP地址就是客户端的外网IP。所以服务器接收到请求后可以得知客户端外网IP，如果服务器把客户端外网IP给我们返回，这样就达到了间接获取外网IP的方法。

所以目前的问题是我们只要找到一些提供返回IP地址服务的网站就可以了。

介绍2个返回IP地址的网站：

淘宝`http://ip.taobao.com/service/getIpInfo2.php?ip=myip`

和

搜狐`http://pv.sohu.com/cityjson?ie=utf-8`

淘宝返回的结果值为：

```json
{"code":0,"data":{"country":"\u4e2d\u56fd","country_id":"CN","area":"\u534e\u5317","area_id":"100000","region":"\u5317\u4eac\u5e02","region_id":"110000","city":"\u5317\u4eac\u5e02","city_id":"110100","county":"","county_id":"-1","isp":"\u9e4f\u535a\u58eb","isp_id":"1000143","ip":"113.47.141.3"}}
```

搜狐返回的结果值为：

```json
var returnCitySN = {"cip": "113.47.141.3", "cid": "110000", "cname": "北京市"};
```

代码实现：

```java
    /**
     * 获取外网IP地址
     *
     * @return
     */
    public static String getNetIp() {
        URL infoUrl = null;
        InputStream inStream = null;
        String line = "";
        try {
            infoUrl = new URL("http://pv.sohu.com/cityjson?ie=utf-8");
            URLConnection connection = infoUrl.openConnection();
            HttpURLConnection httpConnection = (HttpURLConnection) connection;
            int responseCode = httpConnection.getResponseCode();
            if (responseCode == HttpURLConnection.HTTP_OK) {
                inStream = httpConnection.getInputStream();
                BufferedReader reader = new BufferedReader(new InputStreamReader(inStream, "utf-8"));
                StringBuilder strber = new StringBuilder();
                while ((line = reader.readLine()) != null)
                    strber.append(line + "\n");
                inStream.close();
                // 从反馈的结果中提取出IP地址
                int start = strber.indexOf("{");
                int end = strber.indexOf("}");
                String json = strber.substring(start, end + 1);
                if (json != null) {
                    try {
                        JSONObject jsonObject = new JSONObject(json);
                        line = jsonObject.optString("cip");
                    } catch (JSONException e) {
                        e.printStackTrace();
                    }
                }
                return line;
            }
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return line;
    }
```

