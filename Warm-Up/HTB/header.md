# Section 3: What Is an Email Header and How to Read It?

An email header contains routing and message metadata, including fields such as
`From`, `To`, `Date`, `Return-Path`, `Reply-To`, and `Received`. These fields
help an analyst understand who a message claims to be from, where replies are
directed, and which mail servers handled it.

For the local VPN and remote-desktop workflow used in these exercises, see
[Lab setup](./README.md#lab-setup).

## Sample email

The exercise uses **Top 3 Blog posts for SOC teams 👀.eml**, downloaded from the
lab's `Challenge+Mail.zip` archive:

[`Top 3 Blog posts for SOC teams 👀.eml`](./Top%203%20Blog%20posts%20for%20SOC%20teams%20👀.eml)

The archive password is provided in the Academy exercise instructions. The
commands below assume that you are in the directory containing the sample
message.

## Questions and analysis

### 1. If you reply to this email, what address will receive the reply?

The `From` field identifies the address shown as the sender. Compare it with
`Reply-To` to confirm the reply destination:

```shell
grep -iE '^(From|Reply-To):' 'Top 3 Blog posts for SOC teams 👀.eml'
```

The sample has matching `From` and `Reply-To` addresses.

<details>
<summary>Reveal answer</summary>

**Answer:** `info@letsdefend.io`

</details>

### 2. In what year was the email sent?

Read the `Date` field:

```shell
grep -i '^Date:' 'Top 3 Blog posts for SOC teams 👀.eml'
```

The date shown is Monday, 21 March 2022.

<details>
<summary>Reveal answer</summary>

**Answer:** `2022`

</details>

### 3. What is the Message-ID, without the surrounding angle brackets?

The `Message-ID` field identifies this message. Remove only the surrounding
`<` and `>` characters when submitting it:

```shell
grep -i '^Message-ID:' 'Top 3 Blog posts for SOC teams 👀.eml'
```

<details>
<summary>Reveal answer</summary>

**Answer:** `74bda5edf824cea8aad36e707.675c34a61f.20220321204512.a02caaccf3.a268ce5a@mail41.suw13.rsgsv.net`

</details>

Continue to [Section 4: Email Header Analysis](./header-analysis.md).
