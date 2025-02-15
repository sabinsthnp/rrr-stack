# rrr-stack

Video 39 - Deploy ARR apps using just 1 command (full set with Jellyfin and qBittorrent !!!)

Useful Links:
Servarr Wiki
Trash Guides
Ascii ART
Download and unzip Files from GitHub:
https://github.com/automation-avenue/youtube-39-arr-apps-1-click
cd /home/marek/Downloads
unzip youtube-39-arr-apps-1-click

Installation process:
Make sure you are in the same folder as docker-compose.yml and .env file, then 'up' to deploy, 'stop' and 'rm' to stop and remove the stack :

sudo docker-compose up -d 
sudo docker-compose stop
sudo docker-compose rm 
Go to the folder specified in .env file (if its /media/Arr then go to /media as root) and run chown command with the user id and group id configured in that .env file:
chown -R 1000:1000 Arr
Now you can log on and work with all services.

First configure the qBittorrent service because its using temporary password only:

qBittorrent:
First - find the qbittorrent container id by running:
sudo docker ps
Then check logs for that container it:
sudo docker logs <qbittorrent-container-id>
You will see in the logs something like:
The WebUI administrator username is: admin
The WebUI administrator password was not set. A temporary password is provided for this session:
Now you can go to URL:
http://localhost:8080
and log on using details provided in container logs.
Go to Tools - Options - WebUI - change the user and password and tick 'bypass authentication for clients on localhost' .

Then first configure Prowlarr service (each of these services will require to set up user/pass):

Prowlarr:
http://localhost:9696
Go to Settings - Download Clients - + symbol - Add download client - choose qBittorrent (unless you decided touse different download client)
Put the port id matching the WebUI in docker-compose for qBittorrent (default is 8080) and username and password that you configured for qBittorrent in previous step
Host - you have to change from localhost to ip address of the host machine (run 'ip address' command on your host system)

Sonarr:
http://localhost:8989
Go to Settings - Media Management - Add Root Folder - set /data/tvshows as your root folder
Go to Settings - Download Clients - click + symbol - choose qBittorrent and repeat the steps from Prowlarr.
(there are also 'Remote Path Mappings' - use only if your qBittorrent and ARR stack are on different hosts / systems)
Go to Settings - General - scroll down to API key - copy - go to Prowlarr - Settings - Apps -click '+' - Sonarr - paste API key and change 'localhost' to ip address of the Ubuntu/Host again.
Then Settings - General - switch to 'show advanced' in top left corner - scroll down to 'Backups' and choose /data/Backup (or whatever location you have in your docker compose file for Sonarr backups )

Radarr:
http://localhost:7878
Go to Settings - Media Management - Add Root Folder - set /data/movies as your root folder
Then Settings- Download clients - click 'plus' symbol, choose qBittorrent etc - basically same steps as for Sonarr
Settings - General - scroll down to API key - copy - go to Prowlarr - add same way as in sonarr
Settings - General - switch to 'show advanced'- Backups - choose /data/Backup folder

Lidarr:
http://localhost:8686
Follow the same steps for Lidarr and Readarr as for above applications.

Readarr:
http://localhost:8787

Homarr:
http://localhost:7575

Now go back to Prowlarr and click 'Indexers at the top right, click 'Add indexer' - search for sth like 'rarbg' or 'yts' etc then test - save
Then click 'Sync App Indexers icon (next to 'Add indexer')
If you go to Settings - Apps - you should see green 'Full sync' next to each application.
Arr stack completed - you can now 'add movie' in radarr or 'add series' in sonarr etc and click 'search all' or 'search monitored' - that will trigger the download process.

Jellyfin:
http://localhost:8096
If you run docker-compose up and have something running on port 1900 - its most possibly rygel service, run:
sudo apt-get remove rygel and run the sudo docker-compose up -d again.
Then add media library in Jellyfin matching folders configured in docker-compose.yml file, so in Jellyfin you should see them as:
/data/Movies
/data/TVShows
/data/Music
/data/Books