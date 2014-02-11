funz
====

//Shamelessly taken from http://en.wikibooks.org/wiki/C_Programming/Networking_in_UNIX
/*
# Copyright (c) 2013 Eric DANNIELOU
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
# 1. Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in the
#    documentation and/or other materials provided with the distribution.
# 3. Neither the name of Eric DANNIELOU nor the names of contributors
#    may be used to endorse or promote products derived from this software
#    without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY Eric DANNIELOU AND CONTRIBUTORS ``AS IS'' AND
# ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED.  IN NO EVENT SHALL Eric DANNIELOU OR CONTRIBUTORS BE LIABLE
# FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
# DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
# OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
# HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
# LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY
# OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF
# SUCH DAMAGE.


*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>
 
#define PORTNUM 2343
 
int main(int argc, char *argv[])
{
//    char msg[] = "Hello World !\n";
//    char msg[] = popen("/usr/games/fortune", "r");
	char msg[100];
 
    struct sockaddr_in dest; /* socket info about the machine connecting to us */
    struct sockaddr_in serv; /* socket info about our server */
    int mysocket;            /* socket used to listen for incoming connections */
    socklen_t socksize = sizeof(struct sockaddr_in);
 
    memset(&serv, 0, sizeof(serv));           /* zero the struct before filling the fields */
    serv.sin_family = AF_INET;                /* set the type of connection to TCP/IP */
    serv.sin_addr.s_addr = htonl(INADDR_ANY); /* set our address to any interface */
    serv.sin_port = htons(PORTNUM);           /* set the server port number */    
 
    mysocket = socket(AF_INET, SOCK_STREAM, 0);
 
    /* bind serv information to mysocket */
    bind(mysocket, (struct sockaddr *)&serv, sizeof(struct sockaddr));
 
    /* start listening, allowing a queue of up to 1 pending connection */
    listen(mysocket, 1);
    int consocket = accept(mysocket, (struct sockaddr *)&dest, &socksize);
 
    while(consocket)
    {
        printf("Incoming connection from %s - sending welcome\n", inet_ntoa(dest.sin_addr));
	FILE* file = popen("/usr/games/fortune 2>&1", "r");
	fgets(msg, 100, file);
        send(consocket, msg, strlen(msg), 0); 
        consocket = accept(mysocket, (struct sockaddr *)&dest, &socksize);
    }
 
    close(consocket);
    close(mysocket);
    return EXIT_SUCCESS;
}



