FROM adguard/adguardhome

# Add a startup script that dynamically adds the route
RUN echo "#!/bin/bash\n\
if [ -n \"\$WG_ROUTE\" ]; then\n\
  echo \"Adding route: \$WG_ROUTE\"\n\
  ip route add \$WG_ROUTE\n\
else\n\
  echo \"No WG_ROUTE specified, skipping route addition.\"\n\
fi\n\
exec /opt/adguardhome/AdGuardHome \"\$@\"" > /usr/local/bin/start-adguard

# Make the script executable
RUN chmod +x /usr/local/bin/start-adguard

# Set the new entrypoint
ENTRYPOINT ["/usr/local/bin/start-adguard"]

# Preserve the original CMD
CMD [ \
    "--no-check-update", \
    "-c", "/opt/adguardhome/conf/AdGuardHome.yaml", \
    "-w", "/opt/adguardhome/work" \
]