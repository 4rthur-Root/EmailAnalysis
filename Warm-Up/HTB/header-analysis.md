**Section 4:** Email Header Analysis

In the previous section, we looked at what a phishing email is, what the header information is, and what it does. Now, when we suspect that an email is phishing, we will know what we should do and what the analysis process should be like.

I will use the "C:\\Users\\LetsDefend\\Desktop\\Files\\Header-Challenge.zip" file to solve the questions below. File Password: infected. In our case where I downloaded them it is [] 

### Question 1: Are the sender's address and the address in the "Reply-To" area different? Answer Format: Y/N
```shell
grep From May\ God\ Bless\ You...eml

From: "Mrs. Dara Patton"<mrs.dara@jcom.home.ne.jp>
grep Reply-To May\ God\ Bless\ You...eml

Reply-To: <mrs.dara@daum.net>


```
*Answer: Y*

### Question 2: If I want to reply to this email, which address will it be sent to?
```shell
grep Reply-To May\ God\ Bless\ You...eml
Reply-To: <mrs.dara@daum.net>

```

*Answer: mrs.dara@daum.net*

### Question 3: What IP address was the email sent from?

```shell
grep Received May\ God\ Bless\ You...eml

Received: by 2002:a05:7000:4689:0:0:0:0 with SMTP id l9csp4131148map;
X-Received: by 2002:a63:8bc9:0:b0:365:3b6:47fb with SMTP id j192-20020a638bc9000000b0036503b647fbmr17942508pge.147.1645496288442;
Received: from mgw1.mx.zaq.ne.jp (snd01105-jc.im.kddi.ne.jp. [222.227.81.181])
Received-SPF: pass (google.com: domain of mrs.dara@jcom.home.ne.jp designates 222.227.81.181 as permitted sender) client-ip=222.227.81.181;
Received: from mgw1.mx.zaq.ne.jp by osmta1005-jc.im.kddi.ne.jp with ESMTP
Received: from User by omta1005-jc.im.kddi.ne.jp with SMTP

```

*Answer: 222.227.81.181*