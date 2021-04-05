# etl (Extract Transform Load)

This is a set of scripts that repeatably:

1. Extracts the full course schedule for KU from classes.ku.edu
2. Transforms the Excel file output into Postgres-friendly CSV
3. Creates a Postgres table based on the header row and loads the course data into it

## How to use it

```bash
# download a timestamped version of the source Excel file from KU's service
./extract

# transform a file (by default the most recent extracted one if no arg passed) to CSV
./transform [extracted/FILENAME]

# load CSV (by default the most recent transformed one if no arg passed) into Postgres
./load PLATTER_DB_NAME [transformed/FILENAME] [DESIRED_DB_TABLE_NAME]
```

## Compatibility

These scripts were written (pretty quickly 😆) on Mac OS 11.2.3 using Bash as a shell. While I tried
to make most commands both `sh` and cross-OS compatible, I haven't yet had a chance to test on other
OSes. Please feel free to PR more compatible/general versions of these ETL commands, but ensure you
also test on Mac/Bash meanwhile.
