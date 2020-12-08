#!/usr/bin/perl -s
use strict;
use warnings;
  my $Hostname=$ARGV[0];
  my $input = system("curl -Lk  http://verify.cpanel.net/index.cgi?ip=$Hostname >/tmp/cpanelcheck.$$");
  if ($input == 0) {
  my $PREFIX="PROCESS_SERVICE_CHECK_RESULT';'$Hostname ';'DOMAIN CHECK";
  if  (`/bin/grep -iw "The cPanel license server said that your license has been expired" /tmp/cpanelcheck.$$|/bin/grep -Ev "Do"` )
  {
    print " cPanel license expired ";
    exit 2;
  }
  elsif  (`/bin/grep "warning" -iw /tmp/cpanelcheck.$$`)
  {
    print " WARNINGS in $Hostname license.";
    exit 1;
  }
 
  else
  {
    print "cPanel license is active\n";
    exit 0
  }
  system("rm -rf /tmp/cpanelcheck.$$");

  }

  else {
    print "cPanel license status unknown\n";
    exit 2;
  }
