# Hardening step 3: block xmlrpc.php + wp-cron.php

FROM wordpress:latest

LABEL stage="hardening-step-3-block-endpoints"

# Drop info-disclosure files shipped with the WordPress source
RUN rm -f /usr/src/wordpress/readme.html \
          /usr/src/wordpress/license.txt \
          /usr/src/wordpress/wp-config-sample.php

# Apache: hide version, distro, and signature footer on error pages
RUN echo "ServerTokens Prod"    >> /etc/apache2/conf-available/security.conf \
 && echo "ServerSignature Off"  >> /etc/apache2/conf-available/security.conf

# PHP: drop the X-Powered-By header
RUN echo "expose_php = Off" > /usr/local/etc/php/conf.d/hardening.ini

# Apache: deny direct access to high-risk endpoints
RUN { \
      echo "<Location \"/xmlrpc.php\">"; \
      echo "    Require all denied"; \
      echo "</Location>"; \
      echo "<Location \"/wp-cron.php\">"; \
      echo "    Require all denied"; \
      echo "</Location>"; \
    } > /etc/apache2/conf-available/block-endpoints.conf \
 && a2enconf block-endpoints

EXPOSE 80
