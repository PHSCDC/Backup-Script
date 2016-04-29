#!/bin/sh

#defining the archeive directories
DATE=`date +%Y-%m-%d:%H`        #Date for backup storage directories including hour

#Server Values
WWWN=1          #WWW server number value
GAMEN=2         #Game server number value

SERVER="WWW"
TOTAL=2

#Loop to create the directories to store the actual backups

COUNTER=0
while [ $COUNTER =lt $TOTAL ]
do
        COUNTER=`expr $COUNTER + 1`
        
        if [ $COUNTER -eq $GAMEN ]
        then
                SERVER="GAME"
        fi
        
        DNAME=$DATE\_$SERVER
        
        mkdir -p $DNAME
done

                #Command basis for backups:
                #rsync -avz -e ssh remoteuser@remotehost:/remote/dir /this/dir
                
  SERVER="WWW"  #Resetting the SERVER variable to WWW
  
  #Loop the process of updating the backups to actually backup the directories
  
  COUNTER=0
  while [ $COUNTER -lt $TOTAL ]
  do
          COUNTER=`expr $COUNTER + 1`
          
          if [ $COUNTER -eq $GAMEN ]
          then
          #Game server backup
          
          SERVERIP=201.53.2.11
          
          rsync -avz -e ssh backup@$SERVERIP:/home /root/scripts/$DATE\_GAME
          rsync -avz -e ssh backup@$SERVERIP:/root/worlds/root/world /root/scripts/$DATE\_GAME/
          rsync -avz -e ssh backup@$SERVERIP:/var/www/ /root/scripts/$DATE\_GAME/
          
          else
          #Webserver Backup
          
          SERVERIP=201.53.2.10
          
          rsync -avz -e ssh backup@$SERVERIP:/var/www/webapp root/scripts/$DATE\_WWW/webapp
          rsync -avz -e ssh backup@$SERVERIP/home /root/scripts/$DATE\_WWW/home
          #rsync -avz -e ssh backup@$SERVERIP:/Database dump? /storage directory #Notice, I don't know the location of the database dump
          
          
          fi
done

#Should be done, but some is incomplete or not working at the moment
