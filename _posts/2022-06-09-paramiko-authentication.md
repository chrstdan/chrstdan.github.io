---
layout: single
title:  "Solve 'Auhentication Failed' in Paramiko"
header:
<!--   image: "/assets/images/paramiko-auth.png"
  image_description: "Automation Script using Paramiko" -->
date:   2022-06-09 18:11:25 +0300
category: Python
classes: wide
tags:  
- python
---

I got an error when running automation script for network device like this:

{% highlight bash %}

~python-Automation/ $ python3 multivendor.py 

Traceback (most recent call last):
  File "multivendor.py", line 23, in <module>
paramiko.ssh_exception.AuthenticationException: Authentication failed.
  
{% endhighlight %}

so when you have entered the correct password and username but still get an error like that, you need to add this parameter in your automation script:

{% highlight python %}
  
look_for_keys=False
  
{% endhighlight %}

* *look_for_keys:* the parameter will disable search for discoverable private key files in ~/.ssh/.

So you will see my script like this
-----------------------------------
  
{% highlight python %}
import paramiko
import time

devices = [
    {
        'ip_address' : '172.16.1.1',
        'vendor' : 'mikrotik',
        'username' : 'router1',
        'password' : 'tes'
    },
    {
        'ip_address' : '172.16.1.2',
        'vendor' : 'cisco',
        'username' : 'cisco',
        'password' : 'cisco'
    }
]

ssh_client = paramiko.SSHClient()
ssh_client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

for device in devices:
    ssh_client.connect(hostname=device['ip_address'],
                                username=device['username'],
                                password=device['password'],
                                look_for_keys=False)
    print("success login to {}".format(device['ip_address']))
    if device['vendor'] == 'cisco':
        conn = ssh_client.invoke_shell()
        # conn.send("enable\n")
        conn.send("conf t\n")
        conn.send("int lo99\n")
        conn.send("ip add 10.1.99.1 255.255.255.255\n")
        time.sleep(2)
        # output = conn.recv(65535)
        # print(output.decode())
    else:
        ssh_client.exec_command('interface bridge add name=loopback3\n')
        ssh_client.exec_command('ip add add address=10.2.99.2/32 interface=loopback3')

ssh_client.close
  
{% endhighlight %}


  
More information 
-----------------------------------

More information about Paramiko can be found in [paramiko-docs](https://docs.paramiko.org/en/stable/api/client.html)
