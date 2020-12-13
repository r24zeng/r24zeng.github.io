---
title: Socket Programming
tags: computer network
categories: notes
---
## UDPclient.py
> from socket import *
> serverName = ’hostname’
> serverPort = 12000
> clientSocket = socket(AF_INET, SOCK_DGRAM)
> message = raw_input(’Input lowercase sentence:’)
> clientSocket.sendto(message.encode(),(serverName, serverPort))
> modifiedMessage, serverAddress = clientSocket.recvfrom(2048)
> print(modifiedMessage.decode())
> clientSocket.close()

## UDPserver.py
> from socket import *
> serverPort = 12000
> serverSocket = socket(AF_INET, SOCK_DGRAM)
> serverSocket.bind((’’, serverPort))
> print(”The server is ready to receive”)
> while True:
>   message, clientAddress = serverSocket.recvfrom(2048)
>   modifiedMessage = message.decode().upper()
>   serverSocket.sendto(modifiedMessage.encode(), clientAddress)

## TCPclient.py
> from socket import *
> serverName = ’servername’
> serverPort = 12000
> clientSocket = socket(AF_INET, SOCK_STREAM)
> clientSocket.connect((serverName, serverPort))
> sentence = raw_input(’Input lowercase sentence:’)
> clientSocket.send(sentence.encode())
> modifiedSentence = clientSocket.recv(1024)
> print(’From Server: ’, modifiedSentence.decode())
> clientSocket.close()

## TCPserver.py
> from socket import *
> serverPort = 12000
> serverSocket = socket(AF_INET, SOCK_STREAM)
> serverSocket.bind((’’, serverPort))
> serverSocket.listen(1)
> print(’The server is ready to receive’)
> while True:
>   connectionSocket, addr = serverSocket.accept()
>   sentence = connectionSocket.recv(1024).decode()
>   capitalizedSentence = sentence.upper()
>   connectionSocket.send(capitalizedSentence.encode())
>   connectionSocket.close()


> Reference material: 
> Book: Computer Networking A Top-Down Approach 7th edition, Jim Kurose & Keith Ross, Addison-Wesley. 
> Slides: University of Waterloo, ECE 456/656 (Computer Networks), 2020 spring term, Professor Zille Huma Kamal.