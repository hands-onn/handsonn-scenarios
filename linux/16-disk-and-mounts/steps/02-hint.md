/data/cache holds the 20M file, so it's the biggest. `du -sh /data/* | sort -rh | head -1` surfaces it; the check looks for 'cache' in /tmp/big_dir.
