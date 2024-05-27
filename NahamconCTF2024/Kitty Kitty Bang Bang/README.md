<img width="696" alt="dsadsa" src="https://github.com/er4pwn/CTF_writeup/assets/45916763/860b60f8-1de4-48d9-8cd0-0cd1ff9b44ca">
<br>
<br>
We acquired the .apk file and proceeded to decompile it for an in-depth review of the source code.
<br>
Upon examination, we discovered that the application logs messages whenever the screen is tapped, emitting a distinctive 'BANG!' upon each tap. Furthermore, the code includes a function to retrieve the flag from mainActivity.stringFromJNI() and logs it accordingly.
<br>
<br>
<img width="696" alt="dsadsa" src="https://github.com/er4pwn/CTF_writeup/assets/45916763/5298737d-8841-4b2d-b9b5-08a13b3b78eb">
<br>
<br>
To delve deeper into its functionality, we utilized adb to install and launch the .apk. Subsequently, we employed logcat to display the logged messages, providing valuable insights into the application's behavior.
<br>
<br>
<img width="696" alt="dsadsa" src="https://github.com/er4pwn/CTF_writeup/assets/45916763/02c7aad8-c3c5-4e72-856a-069bb0f9a2dd">
<br>
<br>
Reviewing the logcat we got the flag!
<br>
<br>
<img width="696" alt="dsadsa" src="https://github.com/er4pwn/CTF_writeup/assets/45916763/8ca360f1-ad54-49cf-864e-392167cc0d9b">

