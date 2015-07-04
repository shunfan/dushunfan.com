---
title:  通过 ping 命令选择云梯 VPN 最快线路
date:   2014-05-12 09:36:12 +0800
---

[云梯 VPN](http://refyt.com/?r=87c78057d0dcc5a5) 提供了很多条国际线路给用户选择，自己经常不清楚到底应该选择哪条线路以获取最佳效果。于是用 Ruby 写出了一个通过 ping 值比较线路速度的小工具。

使用前请先安装 net-ping:

    gem install net-ping

以下是源代码：

{% highlight ruby %}
require 'net/ping'

def ping_average(host, count = 10)
  result = []
  target = Net::Ping::TCP.new(host, 'http')
  count.times do
    target.ping
    result << target.duration if target.duration
  end
  ((result.reduce(:+) / result.size) * 1000).to_i
end

def vpn_url(server)
  "#{server}.vpnplease.com"
end

vpn_servers = { sg: [:sg1, :sg2],
                jp: [:jp1, :jp2, :jp3],
                us: [:us1, :us2, :us3, :us4, :us5],
                tw: [:tw1],
                hk: [:hk1, :hk2],
                uk: [:uk1] }

ping_results = {}
vpn_servers.map do |location, servers|
  for server in servers
    ping_results[location] ||= {}
    ping_results[location][server] = ping_average(vpn_url(server))
  end
end

ping_report = ""
ping_results.map do |location, location_results|
  best_server = location_results.key(location_results.values.min)
  ping_report += "#{location}:\n"
  location_results.map do |server, server_result|
    if server == best_server
      ping_report += "  #{server}: #{server_result} *\n"
    else
      ping_report += "  #{server}: #{server_result}\n"
    end
  end
end

print ping_report
{% endhighlight %}

Example Result：

    sg:
      sg1: 324
      sg2: 87 *
    jp:
      jp1: 335
      jp2: 184 *
      jp3: 484
    us:
      us1: 540
      us2: 395
      us3: 317
      us4: 188 *
      us5: 415
    tw:
      tw1: 131 *
    hk:
      hk1: 285 *
      hk2: 340
    uk:
      uk1: 299 *
