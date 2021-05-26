FROM wordpress:latest
 
# Install xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug
 
# Install Less for WP-CLI
RUN apt-get update && apt-get -y

# Install nvm nodejs
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
    && nvm install --lts
    
RUN apk add --update \
    python \
    python-dev \
    py-pip \
  && rm -rf /var/cache/apk/*


# Globally node-sass from NPM
RUN npm install --unsafe-perm -g node-sass
 
# Install WP-CLI
RUN curl -s -o /usr/local/bin/wp \
    https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    && chmod +x /usr/local/bin/wp
