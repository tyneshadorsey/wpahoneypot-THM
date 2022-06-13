# Mr. Phisher
Room Link: https://tryhackme.com/room/mrphisher

Writeup by Ty. Dorsey (@wpahoneypot)

## Challenge Scenerio
I recieved a suspicious email with a very weird-looking attachment. It keeps asking me to "enable macros". What are those?

## Task 
Uncover the flag in the email attachment : {}

## Walkthrough
Opening the room, you're shown a folder directory containing both a word document an a zip file (inside the zip file is the same word document)

Opening the word document you're met with the alert from the scenerio. 

![image](https://user-images.githubusercontent.com/36011916/173400980-5d685173-ec4f-49da-9358-0abbe41a8af7.png)

Lets go see what all the fuss is about by viewing the macros ourselves.
> Tools → Macros → Edit Macros
![image](https://user-images.githubusercontent.com/36011916/173401494-a50b00cc-c3fb-42cf-95fd-8b2a389ed0b8.png)

Navigate to the macro for the MrPhisher.docm
> MrPhisher.docm → Project → Modules → NewMacros
![image](https://user-images.githubusercontent.com/36011916/173401957-0ec8cb06-8d8d-415a-bffa-40db294a5d2d.png)

If we break down each line of the code:
```vb
Option VBASupport 1
Sub Format() ' creates a sub procedure (executes a task - doesnt return any values - similar to a void function)'
Dim a() 'creates a list variable named "a"'
Dim b As String  'creates a string variable named "b"'

a = Array(102, 109, 99, 100, 127, 100, 53, 62, 105, 57, 61, 106, 62, 62, 55, 110, 113, 114, 118, 39, 36, 118, 47, 35, 32, 125, 34, 46, 46, 124, 43, 124, 25, 71, 26, 71, 21, 88)

For i = 0 To UBound(a) 'creates a for loop from 0 to 37 (the amount of elemens in a)'
b = b & Chr(a(i) Xor i) 'XORs the nummbers in the array getting the char value and adding it to b'

Next 'continuing'
End Sub 'endoffunction'
```

### Solving
Solving is a matter of finding out what b is 
To do this, I simply created a new document with macros. (You do this so we can isolate and control the code ourselves) 

Pasted in the original function and edited it to print out the b variable by adding this line before the 'End Sub' line.
```vb
Print b
```
![image](https://user-images.githubusercontent.com/36011916/173405322-60d198db-ca25-4afc-bcbf-11da290d4df3.png)

You can execute the code using the green play button and you'll recieve the following alert containing the flag needed to complete the room.
![image](https://user-images.githubusercontent.com/36011916/173406268-7445ed87-5415-49e2-9e96-9c803b0e0528.png)

😊


