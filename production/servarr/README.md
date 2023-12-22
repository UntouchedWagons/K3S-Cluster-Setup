
# Projects in the network Namespace

 * Bazarr - Downloads subtitles
 * Lidarr - Organizes and downloads music
 * Prowlarr - Organizes torrent trackers between Lidarr, Radarr and Sonarr, but not download clients
 * Radarr - Organizes and downloads movies
 * Sonarr - Organizes and downloads TV shows

Normally these projects use SQLite for their database needs but have semi-recently added PostgreSQL support. Converting an install to use PostgreSQL can be a bit fiddly but is worth doing in my opinion. I had numerous performance issues, mainly in prowlarr (of all services) and even corruption of the SQLite database (in Radarr).

Even though several environment variables are set for the bazarr container, they're not actually used annoyingly, so setting it up to use PostgreSQL is extra annoying.
