# perl-dc_log
perl to deal with dc log

Code:

#!/usr/bin/perl
$in=$ARGV[0];
if(!defined $in) {
    die "Usage:$0 filename";
}

my $out=$in;
$out=~s/(\.\w+)?$/.txt/;

if(!open $in_fh, '<',$in) {
    die "cannot open '$in':$!";
}

if(!open $out_fh, '>',$out) {
    die "cannot open '$out':$!";
}

my @error = ();
my @warning = ();

while(<$in_fh>)  {

    chmop;
    if(/^Error/) {
        push @error, $_;
    }
    elsif(/^Warning/) {
        push @warning, $_;
    }
}

my @war_type = ();
my @w = ();
my $flag = 0;

foreach my $war (@warning) {

    $war =~ /(Warning):(.*)\((.*)\)/;
    foreach my $ele (@war_type) {
        if($3 eq $ele) {
            $flag = 1;
            last;
        }
    }
    if($flag == 0) {
        push @war_type, $3;
    }
    $flag = 0;
}

foreach my $ele (@war_type) {
    print $out_fh $ele."\n";
}

foreach my $type (@war_type) {

    if($type eq "") {
        next;
    }
    foreach my $war (@warning) {  
    
        if(/(.*)/) {
            $war =~ /(Warning):(.*)\((.*)\)/;
            if($type eq $3) {
                print $out_fh $war;    
            }
            else {
                next;
            }
        }
    }
}
