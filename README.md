OSM
===

OpenStreetMap

OpenStreetMap is  a free editable map of the world. It was created by Steve Coast in 2004.
One can copy OSM data for free and can use it in their own OpenStreetMap Server.
With OSM server you can make your map look in a way you want.

INSTALLATION

You can either build your tile server manually or through packages.

Build through packages:

For this run these commands on your terminal.

1. sudo apt-get install python-software-properties


Add the repository containing the packages:

2. sudo add-apt-repository ppa:kakrueger/openstreetmap

3. sudo apt-get update

Install the package libapache2-mod-tile and its dependencies through command:

4. sudo apt-get install libapache2-mod-tile

BUILDING TILE SERVER MANUALLY:

1.) If you are building your tile server manually then start by insatlling dependiencies first:

sudo apt-get install subversion git-core tar unzip wget bzip2 build-essential autoconf libtool libxml2-dev libgeos-dev libpq-dev libbz2-dev proj munin-node munin libprotobuf-c0-dev protobuf-c-compiler libfreetype6-dev libpng12-dev libtiff4-dev libicu-dev libboost-all-dev libgdal-dev libcairo-dev libcairomm-1.0-dev apache2 apache2-dev libagg-dev liblua5.2-dev ttf-unifont

2.) Then install postgresql:

sudo apt-get install postgresql-9.1-postgis postgresql-contrib postgresql-server-dev-9.1

3.) Create database for postgis:

sudo -u postgres -i
createuser username # answer yes for superuser (although this isn't strictly necessary)
createdb -E UTF8 -O username gis
exit

Put your username for "username" here.

4.) Set up PostGIS on the postresql database:

psql -f /usr/share/postgresql/9.1/contrib/postgis-1.5/postgis.sql -d gis

5.) Give your newly-created user permission to access some of the PostGIS extensions’ data. Make sure you replace username with your user’s name:

psql -d gis -c "ALTER TABLE geometry_columns OWNER TO username; ALTER TABLE spatial_ref_sys OWNER TO username;"

6.) Install osm2pgsql

mkdir ~/src
cd ~/src
git clone git://github.com/openstreetmap/osm2pgsql.git
cd osm2pgsql
./autogen.sh
./configure
make
sudo make install
psql -f /usr/local/share/osm2pgsql/900913.sql -d gis

7.) Install Mapnik library

cd ~/src
git clone git://github.com/mapnik/mapnik
cd mapnik
git branch 2.0 origin/2.0.x
git checkout 2.0

python scons/scons.py configure INPUT_PLUGINS=all OPTIMIZATION=3 SYSTEM_FONTS=/usr/share/fonts/truetype/
python scons/scons.py
sudo python scons/scons.py install
sudo ldconfig

8.) Install mod_tile and renderd

cd ~/src
git clone git://github.com/openstreetmap/mod_tile.git
cd mod_tile
./autogen.sh
./configure
make
sudo make install
sudo make install-mod_tile
sudo ldconfig

9.) Install Mapnik style-sheet

cd ~/src
svn co http://svn.openstreetmap.org/applications/rendering/mapnik mapnik-style

cd ~/src/mapnik-style
sudo ./get-coastlines.sh /usr/local/share

SOFTWARE CONFIGURATION:

1.) Now you need to do software configuration. for these follow steps given on 
http://switch2osm.org/serving-tiles/manually-building-a-tile-server-12-04/


DOWNLOAD DATA

1.) You can either download your data from  download.geofabrik.de or from JOSM.
   I did it through JOSM.

IMPORT DATA

Now load your data in your newly build tile server:

1.) osm2pgsql --slim -C 1500 bbsbec.osm

2.) sudo touch /var/lib/mod_tile/planet-import-complete

3.) sudo /etc/init.d/renderd restart

If everything worked OK, you should have a working tileserver and you can view its results by going to http://localhost/osm/slippymap.html

Now pat yourself and make changes in files to make your map more beautiful.


Don't forget to clear your tile cache and regentating them again to see your changes.



















