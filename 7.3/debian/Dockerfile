ARG PHP_VERSION=php7.3
FROM jakzal/phpqa:${PHP_VERSION}

LABEL maintainer="hussainweb@gmail.com"

ENV TARGET_DIR="/usr/local/lib/drupalqa"
ENV DRUPAL_CHECK_VERSION="1.0.14"

WORKDIR ${TARGET_DIR}

# PAReview needs the file command. Also install other dependencies.
RUN apt-get update \
    && apt-get install --no-install-recommends -y pkg-config \
    && apt-get install --no-install-recommends -y \
        autoconf \
        build-essential \
        file \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-configure gd --with-freetype-dir --with-jpeg-dir \
    && docker-php-ext-install gd -j$(nproc) \
    && apt-get remove -y autoconf build-essential libfreetype6-dev libjpeg62-turbo-dev libpng-dev pkg-config \
    && rm -rf /var/lib/apt/lists/*

RUN composer require --prefer-dist \
        "squizlabs/php_codesniffer:^3.3" \
        "drupal/coder:^8.3.7" \
        "object-calisthenics/phpcs-calisthenics-rules:^3.5" \
        "phpcompatibility/php-compatibility:^9.3" \
    && \
    git clone https://git.drupalcode.org/sandbox/coltrane-1921926.git drupalsecure && \
    git clone https://github.com/klausi/pareviewsh.git && \
    rm -rf ./drupalsecure/.git/ ./pareviewsh/.git/ && \
    curl -Ls https://github.com/mglaman/drupal-check/releases/download/$DRUPAL_CHECK_VERSION/drupal-check.phar -o $TOOLBOX_TARGET_DIR/drupal-check && chmod +x $TOOLBOX_TARGET_DIR/drupal-check

# Let's remove the phpcs installed by phpqa as it cannot
# autoload classes required in Drupal code sniffs.
RUN rm $TOOLBOX_TARGET_DIR/phpcs && \
    ln -s $TARGET_DIR/vendor/bin/phpcs $TOOLBOX_TARGET_DIR && \
    ln -s $TARGET_DIR/pareviewsh/pareview.sh $TOOLBOX_TARGET_DIR/pareview && \
    chmod +x $TOOLBOX_TARGET_DIR/pareview && \
    phpcs --config-set installed_paths $TARGET_DIR/vendor/drupal/coder/coder_sniffer/,$TARGET_DIR/vendor/phpcompatibility/php-compatibility,$TARGET_DIR/drupalsecure

CMD php $TOOLBOX_TARGET_DIR/toolbox list-tools
