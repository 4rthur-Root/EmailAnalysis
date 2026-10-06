# Section 4: Email Header Analysis

This exercise builds on [Section 3](./header.md) and uses the
`May God Bless You...eml` message from the Academy's `Header-Challenge.zip`
archive. The archive password is provided in the exercise instructions.

[`May God Bless You...eml`](./May%20God%20Bless%20You...eml)

Run the commands below from the directory containing the sample message.

## Questions and analysis

### 1. Are the sender's address and the `Reply-To` address different?

Compare the two fields. A difference means a reply may go to an address other
than the one shown in `From`.

```shell
grep -iE '^(From|Reply-To):' 'May God Bless You...eml'
```

The sample has `mrs.dara@jcom.home.ne.jp` in `From` and
`mrs.dara@daum.net` in `Reply-To`.

<details>
<summary>Reveal answer</summary>

**Answer:** `Y`

</details>

### 2. If you reply to this email, which address will receive the reply?

When present, `Reply-To` specifies the reply destination. Check its value with:

```shell
grep -i '^Reply-To:' 'May God Bless You...eml'
```

<details>
<summary>Reveal answer</summary>

**Answer:** `mrs.dara@daum.net`

</details>

### 3. What IP address was the email sent from?

Inspect the `Received` lines to follow the message's relay path. In this
sample, the external sending host is shown in the line beginning
`Received: from mgw1.mx.zaq.ne.jp`; the same address also appears in the SPF
result.

```shell
grep -iE '^(Received|Received-SPF):' 'May God Bless You...eml'
```

The external sending host shown in the sample is `222.227.81.181`.

<details>
<summary>Reveal answer</summary>

**Answer:** `222.227.81.181`

</details>

The order of `Received` fields matters: mail servers normally prepend their
own trace fields, so read the chain from the bottom upward to follow the
message's path. Header values can be forged; use trusted receiving-server
records and corroborating evidence when drawing conclusions.
