# Dev environment for Wordpresss

## Set up

### Download the environment 

    git clone https://github.com/pcabreus/dev-wordpress.git <project-name>

### Hostname

Set up the hostname in the `docker-compose.yml` file:

      LIVE_URL: "https://<project-name>.com"
      DEV_URL: "http://<project-name>.localhost"
      
Set up a host name pointing at 127.0.0.1 in your /etc/hosts for the dev site.
    
    127.0.0.1 <project-name>.localhost

### Start up

Start up with:

    $ docker-compose up -d
    
### Download the underscores theme

    1. Go to https://underscores.me/. 
    2. Download the theme with a custom name.
    3. Copy the content on `app/wp-content/themes` folder
    4. Create a commit with the new theme.
    5. [Optional] Remove the rest of the themes
    6. Install the dev tool with `yarn` or `npm` inside the installed theme.
    
## Exporting the database

Export the database into a MySQL dump file from the db container:
    
    bin/export-db:
    
    #!/usr/bin/env bash
    this_dir=$(cd `dirname $0` && pwd)
    file="$this_dir/../data/dump.sql"

    # Create dump file
    cmd='exec mysqldump "$MYSQL_DATABASE" -uroot -p"$MYSQL_ROOT_PASSWORD"'
    docker-compose exec db sh -c "$cmd" > $file

    # Remove password warning from the file
    sed -i '.bak' 1,1d $file && rm "$file.bak"
    
Restoring a database dump

    $ bin/restore-db live-dump.sql
    
The restore script:

    bin/restore-db:
    
    #!/usr/bin/env bash
    
    file=$1
    if [ -z "$file" ]; then
        echo "USAGE: restore-db <filename>"
        exit 1;
    fi
    
    # Restore database to db container
    cmd='exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD" "$MYSQL_DATABASE"'
    docker exec -i $(docker-compose ps -q db) sh -c "$cmd" < $file
    
    # Replace LIVE_URL using WP-CLI in wp container
    cmd='wp --allow-root search-replace "$LIVE_URL" "$DEV_URL" --skip-columns=guid'
    docker-compose exec wp sh -c "$cmd"
    
## Reference

https://akrabat.com/developing-wordpress-sites-with-docker/
