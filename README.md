# Country

A simple drupal 8 module that automatically creates `country` vocabulary and prepopulates the terms with all available countries.

# Description

Fields:
- Name (text)
- Official name (text)
- Code2l (text)
- Code3l (text)
- Flag (image)

# Usage

- Download the module.
- Install the module (`drush en country -y`)
- Run (`drush updatedb -y`)


#!/bin/bash
# Database connection information
  dbname=drupal_knowledgehub     # (e.g.: dbname=drupaldb)
  dbhost=db
  dbuser=root # (e.g.: dbuser=drupaluser)
  dbpass=root # (e.g.: dbuser=drupaluser)

# Website Files
#  webrootdir=/var/www/html/docroot  # (e.g.: webrootdir=/home/user/public_html)
  webrootdir=/home/vadim/projects/unccd  # (e.g.: webrootdir=/home/user/public_html)

# Default TAR Output File Base Name
  tarnamebase=sitebackup-
  datestamp=`date +'%Y-%m-%d'`

# Execution directory (script start point)
  startdir=`pwd`

# Temporary Directory
  tempdir=tmpbckdir$datestamp

#
# Input Parameter Check
#

if test "$1" = ""
  then
    tarname=$tarnamebase$datestamp.tgz
  else
    tarname=$1
fi


#
# Banner
#
echo ""
echo "fullsitebackup.sh V1.0"

#
# Create temporary working directory
#
echo " .. Setup"
mkdir $tempdir
echo " .. done"

#
# TAR website files
#
echo " .. TARing website files in $webrootdir"
cd $webrootdir
tar cf $startdir/$tempdir/filecontent.tar .
echo " .. done"

#
# sqldump database information
#
echo " .. sqldump'ing database:"
echo "    user: $dbuser; database: $dbname; host: $dbhost"
cd $startdir/$tempdir
mysqldump -p=dbpass --port=3306 --user=$dbuser --host=$dbhost --add-drop-table $dbname > dbcontent.sql
echo " .. done"

#
# Create final backup file
#
echo " .. Creating final compressed (tgz) TAR file: $tarname"
tar czf $startdir/$tarname filecontent.tar dbcontent.sql
echo " .. done"

#
# Cleanup
#
echo " .. Clean-up"
cd $startdir
rm -r $tempdir
echo " .. done"


#
# Exit banner
#
echo " .. Full site backup complete"
echo ""

