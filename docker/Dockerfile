# Hardening step 2: stop leaking what we run
#
# Removes WP-shipped files that fingerprint the install (readme.html,
# license.txt, wp-config-sample.php) and tightens Apache + PHP so
# response headers and error pages no longer publish versions / distro
# / "powered by" strings.

FROM wordpress:latest

LABEL stage="hardening-step-2-info-disclosure"

# 1. Drop info-disclosure files shipped with the WordPress source
RUN rm -f /usr/src/wordpress/readme.html \
          /usr/src/wordpress/license.txt \
          /usr/src/wordpress/wp-config-sample.php

# 2. Apache: hide version, distro, and signature footer on error pages
RUN echo "ServerTokens Prod"    >> /etc/apache2/conf-available/security.conf \
 && echo "ServerSignature Off"  >> /etc/apache2/conf-available/security.conf

# 3. PHP: drop the X-Powered-By header
RUN echo "expose_php = Off" > /usr/local/etc/php/conf.d/hardening.ini

EXPOSE 80
