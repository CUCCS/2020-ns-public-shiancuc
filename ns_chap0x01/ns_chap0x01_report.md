# 基于 VirtualBox 的网络攻防基础环境搭建

## 实验目的
- 掌握 VirtualBox 虚拟机的安装与使用；
- 掌握 VirtualBox 的虚拟网络类型和按需配置；
- 掌握 VirtualBox 的虚拟硬盘多重加载；

## 实验环境
- VirtualBox 虚拟机
- 攻击者主机（Attacker）：Kali
- 网关（Gateway）：Debian
- 靶机（Victim）：Debian / xp-sp3 / Kali

    ![实验环境](imgs/实验环境.PNG)

## 实验要求
- [x] 虚拟硬盘配置成多重加载
- [x] 网络拓扑图与搭建
- [x] 网络连通性测试
    - [x] 靶机可以直接访问攻击者主机
    - [x] 攻击者主机无法直接访问靶机
    - [x] 网关可以直接访问攻击者主机和靶机
    - [x] 靶机的所有对外上下行流量必须经过网关
    - [x] 所有节点均可以访问互联网

## 实验过程

### 虚拟硬盘配置成多重加载
- 打开虚拟机管理
- 打开虚拟介质管理
- 选中对应虚拟盘，改为多重加载，释放盘片

    ![多重加载](https://github.com/CUCCS/2020-ns-public-shiancuc/blob/ns_chap0x01/ns_chap0x01/imgs/%E5%A4%9A%E9%87%8D%E5%8A%A0%E8%BD%BD.PNG)

***

### 网络拓扑图与搭建

- 本次实验需要用到六台虚拟机，分别为两台XP系统，两台Debian系统以及两台Kali系统，一台Kali作为攻击者，一台Debian作为网关，剩下四台分别在两个局域网内，均作为靶机。
- VirtualBox的网络类型

  > | 网络类型  | 虚拟机对主机的访问 | 主机对虚拟机的访问 | 虚拟机对其他主机的访问 | 其他主机对虚拟机的访问 |  虚拟机之间  |
  > | :-------: | :----------------: | :----------------: | :--------------------: | :--------------------: | :----------: |
  > |  NAT网络  |         √          |         ×          |           √            |           ×            |      ×       |
  > |   网桥    |         √          |         √          |           √            |           √            |      √       |
  > |   内网    |         ×          |         ×          |           ×            |           ×            | 同一网络可以 |
  > | Host-only |         ×          |         ×          |           ×            |           ×            |      √       |
  >

- 配置拓扑环境需要的各网络
    - 网关所需网卡：NAT网络（为了使网关可以访问攻击者主机），Host-only网络（可以进行ssh连接），两块内部网络（分别用于搭建两块独立的局域网）

        ![0.1Debian网关网卡配置](imgs/0.1Debian网关网卡配置.PNG)![0.1Debian网关网卡地址](imgs/0.1Debian网关网卡地址.PNG)

    - 攻击者所需网卡：NAT网络

        ![0.2Kali攻击者网卡配置](imgs/0.2Kali攻击者网卡配置.PNG)![0.2Kali攻击者网卡地址](imgs/0.2Kali攻击者网卡地址.PNG)

    - victim（被攻击者）所需网卡：均为内部网络，使其分为两组，分别在不同的局域网内。

        - Victim-Kali-1

            ![0.3Victim-Kali-1网卡配置](imgs/0.3Victim-Kali-1网卡配置.PNG)![0.3Victim-Kali-1网卡地址](imgs/0.3Victim-Kali-1网卡地址.PNG)

        - Victim-XP-1

            ![0.4Victim-XP-1网卡配置](imgs/0.4Victim-XP-1网卡配置.PNG)![0.4Victim-XP-1网卡地址](imgs/0.4Victim-XP-1网卡地址.PNG)

        - Victim-Debian-2

            ![0.5Victim-Debian-2网卡配置](imgs/0.5Victim-Debian-2网卡配置.PNG)![0.5Victim-Debian-2网卡地址](imgs/0.5Victim-Debian-2网卡地址.PNG)

        - Victim-XP-2

            ![0.6Victim-XP-2网卡配置](imgs/0.6Victim-XP-2网卡配置.PNG)![0.6Victim-XP-2网卡地址](imgs/0.6Victim-XP-2网卡地址.PNG)


### 网络连通性测试

#### 靶机可以直接访问攻击者主机

- Victim-Kali-1
    ![1.1Victim-Kali-1访问攻击者](imgs/1.1Victim-Kali-1访问攻击者.PNG)

- Victim-XP-1
    ![1.2Victim-XP-1访问攻击者](imgs/1.2Victim-XP-1访问攻击者.PNG)

- Victim-Debian-2
    ![1.3Victim-Debian-2访问攻击者](imgs/1.3Victim-Debian-2访问攻击者.PNG)

- Victim-XP-2
    ![1.4Victim-XP-2访问攻击者](imgs/1.4Victim-XP-2访问攻击者.PNG)


#### 攻击者主机无法直接访问靶机

- Kali-Attacker
    - 攻击者可以访问互联网，说明不是网络问题，攻击者无法访问靶机。

    ![2攻击者无法访问靶机](imgs/2攻击者无法访问靶机.PNG)


#### 网关可以直接访问攻击者主机和靶机

- Debian-Gateway

    ![3.1Debian网关可以直接访问攻击者和靶机](imgs/3.1Debian网关可以直接访问攻击者和靶机.PNG)

    - 网关一开始访问不了XP靶机，原因是XP靶机的防火墙没有禁用。

    ![3.2XP系统关闭防火墙](imgs/3.2XP系统关闭防火墙.PNG)

    ![3.3Debian网关可以直接访问攻击者和靶机](imgs/3.3Debian网关可以直接访问攻击者和靶机.PNG)


#### 靶机的所有对外上下行流量必须经过网关

- Victim-Kali-1
    ![4.1Victim-Kali-1流量经过网关](imgs/4.1Victim-Kali-1流量经过网关.PNG)

- Victim-XP-1
    ![4.2Victim-XP-1流量经过网关](imgs/4.2Victim-XP-1流量经过网关.PNG)

- Victim-Debian-2
    ![4.3Victim-Debian-2流量经过网关](imgs/4.3Victim-Debian-2流量经过网关.PNG)

- Victim-XP-2
    ![4.4Victim-XP-2流量经过网关](imgs/4.4Victim-XP-2流量经过网关.PNG)

- 关闭网关后不能访问互联网，并且如图所示，此时的IP地址是自动获取的。
    ![4.5证实流量经过网关](imgs/4.5证实流量经过网关.PNG)


#### 所有节点均可以访问互联网

- Victim-Kali-1
    ![5.1Victim-Kali-1访问互联网](imgs/5.1Victim-Kali-1访问互联网.PNG)

- Victim-XP-1
    ![5.2Victim-XP-1访问互联网](imgs/5.2Victim-XP-1访问互联网.PNG)

- Victim-Debian-2
    ![5.3Victim-Debian-2访问互联网](imgs/5.3Victim-Debian-2访问互联网.PNG)

- Victim-XP-2
    ![5.4Victim-XP-2访问互联网](imgs/5.4Victim-XP-2访问互联网.PNG)

- Debian-Gateway
    ![5.5Debian网关访问互联网](imgs/5.5Debian网关访问互联网.PNG)

- Kali-Attacker
    ![5.6Kali攻击者访问互联网](imgs/5.6Kali攻击者访问互联网.PNG)


## 问题

- 安装Debian系统时，```arp-get```一直报错，更改软件源也没有用，后来才发现是网卡只有lo一块，enp0s3也没有配置。


## 参考资料

- [基于 VirtualBox 的网络攻防基础环境搭建](https://c4pr1c3.github.io/cuc-ns/chap0x01/exp.html)

- [2019-NS-Public-chencwx/ns_chapter1/基于virtualbox的网络攻防基础环境搭建.md](https://github.com/CUCCS/2019-NS-Public-chencwx/blob/ns_chap0x01/ns_chapter1/%E5%9F%BA%E4%BA%8Evirtualbox%E7%9A%84%E7%BD%91%E7%BB%9C%E6%94%BB%E9%98%B2%E5%9F%BA%E7%A1%80%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA.md)

- [2019-NS-Public-YanhuiJessica/ns-0x01/README.md](https://github.com/CUCCS/2019-NS-Public-YanhuiJessica/blob/ns0x01/ns-0x01/README.md)
