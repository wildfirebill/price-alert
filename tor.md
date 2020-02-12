1. generate password
   
```
$ tor --hash-password atmppasswd
16:AA7DC16B3993AF5F608D8C2A4F2389DB36827D6A8A918D3AE2B622793A
```

2. config password

```
$ sudo vi /etc/tor/torrc 
## /usr/local/etc/tor/torrc on osx
HashedControlPassword 16:AA7DC16B3993AF5F608D8C2A4F2389DB36827D6A8A918D3AE2B622793A
ControlPort 9051
```
3. example code
```
import requests
import time
from stem import Signal
from stem.control import Controller

def get_current_ip():
    session = requests.session()

    # TO Request URL with SOCKS over TOR
    session.proxies = {}
    session.proxies['http']='socks5h://localhost:9050'
    session.proxies['https']='socks5h://localhost:9050'

    try:
        r = session.get('http://httpbin.org/ip')
    except Exception as e:
        print str(e)
    else:
        return r.text

def renew_tor_ip():
    with Controller.from_port(port = 9051) as controller:
        controller.authenticate(password="MyStr0n9P#D")
        controller.signal(Signal.NEWNYM)

if __name__ == "__main__":
    for i in range(5):
        print get_current_ip()
        renew_tor_ip()
        time.sleep(5)
```

ref: <https://techmonger.github.io/68/tor-new-ip-python/>