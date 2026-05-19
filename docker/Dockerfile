# Hardening step 1: bump WordPress base image
#
# Previous baseline: wordpress:5.7-apache (2021, 43+ known core CVEs)
# This change: wordpress:latest — current stable WP, current Apache, current PHP.
# Expected effect on scan: the WP-core vulnerability list should drop to ~zero,

FROM wordpress:latest

LABEL stage="hardening-step-1-base-bump"

EXPOSE 80