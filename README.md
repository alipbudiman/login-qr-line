# login-qr-line
login qr using BEAPI in one function

get apikey on https://api.chstore.me/

exmp:
```PY
import requests


BEAPI = "https://api.chstore.me/v1###apikey from >> https://api.chstore.me/"

def QRLogin(header="IOSIPAD\t10.5.2\tiPhone 8\t11.2.5",region="JP",cert=None):
    if BEAPI.split("###")[1] != "apikey from >> https://api.chstore.me/":
        params = {
            "appname": header, # ajust your header
            "country": region, #['HK', 'IN', 'ID', 'JP', 'KR', 'SA', 'SG', 'TH', 'US', 'MY'] ajust region ip login
            "apikey": BEAPI.split("###")[1],
            "cret":cert,
        }
        resp = requests.session().get(BEAPI.split("###")[0]+"/lineqr",params=params).json()
        if resp["status"] == 200:
            if params["cret"] == None:
                print("Link QR: "+resp["result"]["qrlink"]) # or change with line.sendMessage(ro, "Link QR: "+resp["result"]["qrlink"])
                print("QR Image: "+resp["result"]["qrcode"]) # or change with line.sendImageWtihURL(ro, resp["result"]["qrcode"])
                resp2 = requests.session().get(BEAPI.split("###")[0]+"/lineqr/pincode/"+resp["result"]["session"],params={"apikey":BEAPI.split("###")[1]}).json()
                if resp2["status"] == 200:
                    print("Pincode: "+resp2["result"]["pincode"]) # or change with line.sendMessage(ro, "Link QR: "+resp2["result"]["pincode"])
                else:
                    raise Exception (resp["reason"])
            resp3 = requests.session().get(BEAPI.split("###")[0]+"/lineqr/auth/"+resp["result"]["session"],params={"apikey":BEAPI.split("###")[1]}).json()
            if resp["status"] == 200:
                print("Cert: "+resp3["result"]["certificate"]) # you can save this cert code to your database, and added to your parameters if field is not empty
                return resp3["result"]["accessToken"]
            else:
                raise Exception (resp["reason"])
        else:
            raise Exception (resp["reason"])
    else:
        raise Exception ("Please set your BEAPI apikey, get apikey at >> https://api.chstore.me/")

QRLogin()
#exampel fro bot line:

cert = None
cmd = "commands / text"
if cmd == "login": #login default headers
    token = QRLogin(cert=cert)
elif cmd == "login desktopwin": #login using another headers
    token = QRLogin("DESKTOPWIN HEADERS",cert=cert)
elif cmd == "login desktopwin KR": #costumize login
    token = QRLogin("DESKTOPWIN HEADERS",region="KR",cert=cert)

if token == None:
    print("Fail Login")
else:
    print("AccessToken: "+token) # or using os system for login using this token
```
