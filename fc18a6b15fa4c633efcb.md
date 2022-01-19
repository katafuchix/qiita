<!--
title:   Perl DB定義書出力プログラム
tags:    MySQL,Perl,postgerSQL
id:      fc18a6b15fa4c633efcb
private: false
-->
## PerlでDB定義書エクセルファイルを作成
Perlモジュールを使ってエクセルファイルを作ります
ソースはこんな感じ。
設定を変えればPostgreSQL, MySQL 両方で使えます

```
#!C:\Perl\bin\perl
#!/usr/local/bin/perl

use strict;
use DBI;
use Jcode;
use Spreadsheet::WriteExcel;
use DBIx::DBSchema;

#設定
my $DSN	= 'dbi:mysql:DB名:サーバー名';
my $DB_USER = 'ユーザー名';
my $DB_PASSWD	= 'パスワード';
my $XLS_FILE	= '出力ファイル名';

my $dbh = DBI->connect($DSN, $DB_USER, $DB_PASSWD) or die $DBI::errstr;
my $xls = Spreadsheet::WriteExcel->new($XLS_FILE);
my $schema = new_native DBIx::DBSchema $dbh;

for my $table ($schema->tables()) {
    my $work = $xls->addworksheet("$table");
    
    $work->write(0, 0, 'INDEXES');
    my $index_ref = $dbh->selectall_arrayref("SHOW INDEX FROM $table");
    write_worksheet($work, $index_ref, 1);
    
    $work->write(3 + $#{$index_ref}, 0, 'COLUMNS');
    my $column_ref = $dbh->selectall_arrayref("DESCRIBE $table");
    write_worksheet($work, $column_ref, 4 + $#{$index_ref});
}

$dbh->disconnect;


sub write_worksheet {
    my($work, $ref, $offset) = @_;
    for my $row (0 .. $#{$ref}) {
        for my $col (0 .. $#{$ref->[$row]}) {
            $work->write($row + $offset, $col, $ref->[$row]->[$col]);
        }
    }
}
```