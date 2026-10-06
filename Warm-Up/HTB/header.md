# **Section 3:** What is an Email Header and How to Read Them?

The header is a section of the email containing information such as sender, recipient, and date. There are also components such as 'Return-Path', 'Reply-To', and 'Received'

For the rest I use my own machine instead of the one provided by hackthebox , connect using openvpn.
```shell
sudo openvpn /path/to/downloaded/openvpn/folder/
```

Verify with 
```shell
ip a
```

![ip](./evidences/IP.png)

And then start the target machine and connect to it using the command (xfreerdp should be installed first but prompted if not, surely).

```shell
xfreerdp /v:10.129.194.171 /u:letsdefend /p: /d:. /dynamic-resolution
```

![connection](./evidences/rdp.png)

Because of laggs I downloaded the .eml using
```shell
xfreerdp /v:10.129.194.171 /u:letsdefend /p: /d:. \
  /dynamic-resolution /scale-desktop:150 /drive:stuff,$HOME/My_codes_and_Projects/EmailAnalysis/Warm-Up/HTB/header/

```
and in file explorer under this pc I copy pasted

<<Note: You can use the clipboard to paste data to the lab machine or copy data from the lab machine. Note: Use the "C:\\Users\\LetsDefend\\Desktop\\Files\\Challenge+Mail.zip" file to solve the questions below. File Password: infected>>

In the machine it is clear but as I downloaded them , in local this section focuses on 
[*Top 3 Blog posts for SOC teams 👀.eml*](./Top%203%20Blog%20posts%20for%20SOC%20teams%20👀.eml) 

### Question 1 : If we wanted to respond to this email, what would be the recipient's address?


Recipient's address to respond to is clearly the sender of this email.

So
```shell
grep From Top\ 3\ Blog\ posts\ for\ SOC\ teams\ 👀.eml
	h=Subject:From:Reply-To:To:Date:Message-ID:List-ID:List-Unsubscribe:
From: =?utf-8?Q?LetsDefend?= <info@letsdefend.io>

```

*Answer*: info@letsdefend.io

### Question 2: What year was the email sent?

```shell
grep Date Top\ 3\ Blog\ posts\ for\ SOC\ teams\ 👀.eml
	h=Subject:From:Reply-To:To:Date:Message-ID:List-ID:List-Unsubscribe:
	 List-Unsubscribe-Post:Content-Type:MIME-Version:CC:Date:Subject;
Date: Mon, 21 Mar 2022 20:45:17 +0000

```

*Answer*:<details><summary>*Answer :*</summary>2022</details>

### Question 3: What is the Message-ID? (without > < )

```shell
grep -i "^Message-Id:" Top\ 3\ Blog\ posts\ for\ SOC\ teams\ 👀.eml
Message-ID: <74bda5edf824cea8aad36e707.675c34a61f.20220321204512.a02caaccf3.a268ce5a@mail41.suw13.rsgsv.net>

```

*Answer* : 74bda5edf824cea8aad36e707.675c34a61f.20220321204512.a02caaccf3.a268ce5a@mail41.suw13.rsgsv.net


Now for section 4 