---
layout: post
title: "Using Icinga2 to monitor remote M$ Windows Server"
description: ""
category: [tech, administration]
tags: [icinga2]
---
{% include JB/setup %}

首先感谢 github.com/Icinga/icinga2 。

今天精神状态不错，现在对 icinga2 的配置文件已是“了然于胸”——夸口ing 。

第一步，需要在远端服务器安装SNMP服务。同时，最好也安装 icinga2 的 agent 服务，这个在 icinga2 的网页上有 for M$.Win 的安装程序可以下载。

第二步，配置管理服务器的 Icinga2 。比如现在需要监测一个在 192.168.0.19 服务器上运行的 IIS 服务 web 发布进程，对于 管理服务器的 icinga2 来说需要修改或者添加的几个配置文件如下。

在 icinga2.conf 里添加 \<manubulon\> ，这样才能使用SNMP插件。

icinga2.conf

``````
... ...

include <itl>
include <plugins>

include <manubulon>

... ...
``````

新建一个 http_server.conf ，建立一个管理服务器 icinga2 的
"http_server" 和被监测服务器这个 Host 的对应关系。

http_server.conf

``````
object Host "http_server" {
  import "generic-host"

  address = "192.168.0.19"

  vars.os = "Windows"
  vars.sla = "24x7"

}
``````

在 commands.conf 里添加一个命令对象（object）

commands.conf

``````
... ...

object CheckCommand "my-snmp-process" {
  import "plugin-check-command"

  command = [ PluginDir + "/check_snmp_process.pl" ]
  arguments = {
    "-H" = "$snmp_host$"
    "-C" = "$snmp_group$"
    "-p" = "$snmp_port$"
    "-2" = {
        set_if = "$snmp_v2$"
    }
    "-n" = "$procs_name$"
    "-m" = "$procs_mem_warn$,$procs_mem_crit$"
    "-F" = {
        set_if = "$snmp_perf$"
    }
  }

}
``````

最后是新建一个 snmp-process.conf ，明确列出需要被监测的远端服务器进程。下面这段，建立两个 icinga2 Service object，来分别监测远端服务器 aspnet_stat.exe 和 w3wp.exe 进程。

snmp-process.conf

``````
object Service "snmp_p_aspnet_stat" {
  import "generic-service"

  host_name = "http_server"

  check_command = "my-snmp-process"

  vars.snmp_host = "192.168.0.19"
  vars.snmp_group = "XDZLS"
  vars.snmp_port = "161"
  vars.procs_name = "aspnet_stat"
  vars.snmp_v2 = true
  vars.snmp_perf = true
  vars.procs_mem_warn = "900"
  vars.procs_mem_crit = "1200"

  vars.sla = "24x7"
}

object Service "snmp_p_w3wp" {
  import "generic-service"

  host_name = "http_server"

  check_command = "my-snmp-process"

  vars.snmp_host = "192.168.0.19"
  vars.snmp_group = "XDZLS"
  vars.snmp_port = "161"
  vars.procs_name = "w3wp"
  vars.snmp_v2 = true
  vars.snmp_perf = true
  vars.procs_mem_warn = "500"
  vars.procs_mem_crit = "800"

  vars.sla = "24x7"
}
``````

最后重新装载自己linux管理器上 icinga2 的配置文件

``````
  # service icinga2 reload
``````
或者
``````
  # service icinga2 restart
``````

万一在 reload 或者 restart 以后 icinga2 服务报错，可以去 icinga2 的 /var/... .../*.log 文件里去查看细节。

PS. 

个人是很喜欢 VIM 里面的彩色高亮语法显示, 所以特意把 icinga2 配置文件的 VIM Syntax highlight plugin 从 (Icinga/icing2)[https://github.com/Icinga/icinga2] 单独 fork 到 (icinga2-vim)[https://github.com/yeahnoob/icinga2-vim] 。

这样以后，用 Vundle 插件安装针对 icinga2 xxx.conf 的语法高亮能方便一些，直接在装了 Vundle 的 ~/.vimrc 里加个 `` Plugin 'yeahnoob/icinga2-vim' `` 就 OK 。
