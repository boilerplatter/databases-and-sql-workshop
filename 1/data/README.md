# 1/data

Getting data suitable for a database can be hard, dirty work! The more often you suspect you'll do it for any particular dataset, the more important reproducibility becomes. For the purposes of this workshop, though, we be haxing. I wrote the steps down to get each file in this directory just to show you how dirty/hilarious the job can get.

1. `course_codes_and_names.csv` (this will become a `subjects` table)
  a. mouse drag and copy "Subjects" table from https://sis.ku.edu/course-catalog-codes
  b. open sheets.new in a browser tab (new Google Spreadsheet) and paste data
  c. Find and replace (Cmd-Shift-h): Find `[^(]*\((.*)\)$` Replace `$1`. This cleans up subject names.*
  d. Insert a header row (`code` and `subject`)
  e. Download as CSV

* first attempt was `.*\((.*)\)`, which failed for several names with nested parentheses, such as `NRSG (Nursing (Graduate))`

Note this dataset is overlapping with the subjects that can be derived from the Fall '21 course dataset, which we will resolve at the end of section 1 of this workshop.
