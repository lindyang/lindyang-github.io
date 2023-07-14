---
title: "Expect"
date: 2023-07-14T17:25:41+08:00
tags: [bash]
---

```
#!/usr/bin/env expect

set ip [lindex $argv 0]
set user [lindex $argv 1]
set password [lindex $argv 2]
set bin [lindex $argv 3]
set cfck [lindex [file split $bin] end]
set port [lindex $argv 4]
# StrictHostKeyChecking
set ssh_opt "-o UserKnownHostsFile=/dev/null"

log_user 0
set timeout 10
spawn scp $ssh_opt -P $port $bin $user@$ip:/tmp/$cfck
expect {
	"yes/no" { send "yes\r"; exp_continue }
	"*?assword" { send "$password\r" }
	"No route to host" { send_error "无法连接\n"; exit 255 }
	timeout { send_error "scp 连接超时\n"; exit 2 }
}
expect {
  -re "assword: *$" { send_error "密码错误\n"; exit 255 }
   "100%"
}

set timeout 60
spawn -noecho ssh $ssh_opt -p $port $user@$ip "/tmp/kk$cfck;code=\$?;rm -f /tmp/$cfck;exit \$code"
log_user 0
expect {
	"yes/no" { send "yes\r"; exp_continue }
	"*?assword" { send "$password\r" }
	"No route to host" { send_error "无法连接\n"; exit 255 }
	timeout { send_error "ssh 连接超时\n"; exit 2 }
}
log_user 1
expect {
  -re "assword: *$" { send_error "密码错误\n"; exit 255 }
  eof {
    set result [wait]
    set code [lindex $result end]
    # catch wait result
    if {$code != 0} {
      send_error "code=$code; err=[string range $expect_out(buffer) 4 end]"
      exit $code
    }
  }
}
```

```
expect <<EOF
set passwds {foo bar baz}
set i 0
spawn ssh -t root@$server_address "$*"
expect {
    "continue connecting (yes/no)?" { send "yes\r"; exp_continue }
    " password: " { send "[lindex $passwds $i]\r"; incr i; exp_continue }
    eof
}
EOF
```
