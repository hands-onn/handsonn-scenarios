10.0.0.1 appears 4 times — the most. The pipeline `cut -d' ' -f1 | sort | uniq -c | sort -rn | head -1` surfaces it; `awk '{print $2}'` drops the count, leaving the IP.
