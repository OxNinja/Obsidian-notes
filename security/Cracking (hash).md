#cracking #hash
## Cewl
`cewl http://<targetip>/ -m 6 -w cewl.txt`
`wc -l cewl.txt`
`john --wordlist=cewl.txt --rules --stdout > mutated.txt`
`wc mutated.txt`
`medusa -h <targetip> -u admin -P mutated.txt -M http -n 80 -m DIR:/directory/to/login/panel -T 30`

---

## Hydra

`hydra -l root -P /usr/share/wordlısts/rockyou.txt <targetip> ssh`
`hydra -L userlist.txt -P /usr/share/wordlısts/rockyou.txt <targetip> -s 22 ssh -V`

## crack web passwords
http-post-form can change as user module changes
Invalid: what message does the page give for wrong creds
for parameters check with burp

`hydra -l admin -P wordlist.txt <targetip> http-post-form "/login.php:username=^USER^&password=^PASS^:Invalid" -t 64` 

---

## Medusa

`medusa -h <targetip> -u admin -P rockyou.txt -M http -m DIR:/test -T 10

---

## Hashcat

> learn the hash type from hashcat.net example hashes page and pass as its m value

or you can learn with the following command
`hashcat -h | grep -i lm
`hashcat -m 1600 hashes /usr/share/wordlists/rockyou.txt

---

## LM/NTLM
`hashcat -h | grep -i lm 
`hashcat -m 3000  hashes --rules --wordlist=/usr/share/wordlists/rockyou.txt`

https://hashkiller.co.uk/

---

When you find some digits, check if it's 32 bit
`echo -n ....... | wc -c`

------------------------------------------
## John
`john hashes.txt --rules --wordlist=/usr/share/wordlists/rockyou.txt`
