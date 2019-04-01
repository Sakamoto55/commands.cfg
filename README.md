###############################################################################
# COMMANDS.CFG - SAMPLE COMMAND DEFINITIONS FOR NAGIOS 3.0.2
###############################################################################


################################################################################
# NOTIFICATION COMMANDS
################################################################################

# /usr/sbin/sendmail -f nagios@relay01.care2.com email@provider.com
# 'notify-host-by-email' command definition
define command{
	command_name	notify-host-by-email
	#command_line	/usr/bin/printf "%b ***** Nagios *****\n\nNotification Type: $NOTIFICATIONTYPE$\nHost: $HOSTNAME$\nState: $HOSTSTATE$\nAddress: $HOSTADDRESS$\nInfo: $HOSTOUTPUT$\n\nDate/Time: $LONGDATETIME$\n" | /bin/mail -s "** $NOTIFICATIONTYPE$ Host Alert: $HOSTNAME$ is $HOSTSTATE$ **" $CONTACTEMAIL$
	command_line	/usr/bin/printf "Subject: $HOSTSTATE$: $HOSTNAME$ is $HOSTSTATE$\n**Nagios $NOTIFICATIONTYPE$\nHost: $HOSTNAME$ is $HOSTSTATE$\nAddress:$HOSTADDRESS$\nInfo: $HOSTOUTPUT$\nTimestamp: $LONGDATETIME$\n" | /usr/sbin/sendmail -f nagios@care2team.com $CONTACTEMAIL$
	}

# 'notify-service-by-email' command definition
define command{
	command_name	notify-service-by-email
#	command_line	/usr/bin/printf "%b Nagios Notification Type: $NOTIFICATIONTYPE$\nService: $SERVICEDESC$ is $SERVICESTATE$\nHost: $HOSTALIAS$\nAddress: $HOSTADDRESS$\nDate/Time: $LONGDATETIME$\nAdditional Info:\n$SERVICEOUTPUT$\n\nURL: $SERVICENOTESURL$" | /bin/mail -s "** $NOTIFICATIONTYPE$ Service Alert: $HOSTALIAS$/$SERVICEDESC$ is $SERVICESTATE$ **" $CONTACTEMAIL$
	command_line 	/usr/bin/printf "Subject:$SERVICESTATE$: $SERVICEDESC$ on $HOSTALIAS$ is $SERVICESTATE$\n**Nagios** $NOTIFICATIONTYPE$\n$SERVICEDESC$ is $SERVICESTATE$\nHost: $HOSTALIAS$ ($HOSTADDRESS$)\nInfo:$SERVICEOUTPUT$\nNotes: $SERVICEACKCOMMENT$\nTimestamp: $LONGDATETIME$\n" | /usr/sbin/sendmail -f nagios@nagios.care2.com $CONTACTEMAIL$

	}

################################################################################
# HOST CHECK COMMANDS
################################################################################


# This command checks to see if a host is "alive" by pinging it
# The check must result in a 100% packet loss or 5 second (5000ms) round trip 
# average time to produce a critical error.
# Note: Five ICMP echo packets are sent (determined by the '-p 5' argument)

# 'check-host-alive' command definition
define command{
        command_name    PING
        command_line    $USER1$/check_ping -H $HOSTADDRESS$ -w 3000.0,80% -c 5000.0,100% -p 5
        }



################################################################################
# SERVICE CHECK COMMANDS
################################################################################

define command{
	command_name	check_crond
	command_line	$USER1$/check_ntp -H $HOSTADDRESS$ -c $ARG2$
}

define command{
	command_name	check_ntp
	command_line	$USER1$/check_ntp -H $HOSTADDRESS$ -w $ARG1$ -c $ARG2$
}

define command{
	command_name	check_mysql
	command_line	$USER1$/check_tcp -H $HOSTADDRESS$ -p $ARG1$ $ARG2$
}

define command{
	command_name	httpd_procs
	command_line	$USER1$/check_procs -w 10 -C httpd
}

# 'check_local_disk' command definition
define command{
        command_name    check_local_disk
        command_line    $USER1$/check_disk -w $ARG1$ -c $ARG2$ -p $ARG3$
        }


# 'check_local_load' command definition
define command{
        command_name    check_local_load
        command_line    $USER1$/check_load -w $ARG1$ -c $ARG2$
        }


# 'check_local_procs' command definition
define command{
        command_name    check_local_procs
        command_line    $USER1$/check_procs -w $ARG1$ -c $ARG2$ -s $ARG3$
        }


# 'check_local_users' command definition
define command{
        command_name    check_local_users
        command_line    $USER1$/check_users -w $ARG1$ -c $ARG2$
        }


# 'check_local_swap' command definition
define command{
	command_name	check_local_swap
	command_line	$USER1$/check_swap -w $ARG1$ -c $ARG2$
	}


# 'check_local_mrtgtraf' command definition
define command{
	command_name	check_local_mrtgtraf
	command_line	$USER1$/check_mrtgtraf -F $ARG1$ -a $ARG2$ -w $ARG3$ -c $ARG4$ -e $ARG5$
	}

define command{
	command_name	check_dns
	command_line	$USER1$/check_dns -H $ARG1$ -s $HOSTADDRESS$ -a $ARG2$ -t $ARG3$
}

define command{
	command_name	check_ldap
	command_line	$USER1$/check_ldap -H $HOSTADDRESS$ -a $ARG1$ -b $ARG2$ -3 -w $ARG4$ -c $ARG5$ -t 30
}
################################################################################
# NOTE:  The following 'check_...' commands are used to monitor services on
#        both local and remote hosts.
################################################################################


# 'check_ftp' command definition
define command{
        command_name    check_ftp
        command_line    $USER1$/check_ftp -H $HOSTADDRESS$ $ARG1$
        }


# 'check_hpjd' command definition
define command{
        command_name    check_hpjd
        command_line    $USER1$/check_hpjd -H $HOSTADDRESS$ $ARG1$
        }


# 'check_snmp' command definition
define command{
        command_name    check_snmp
        command_line    $USER1$/check_snmp -H $HOSTADDRESS$ $ARG1$
        }

# Command definition for Network Technologies Inc SNMP plugin
 define command{
       command_name    check_nti_snmp
       command_line    $USER1$/check_nti_snmp.pl -H $HOSTADDRESS$ $ARG1$
       }

# 'check_http' command definition
define command{
        command_name    check_http
        command_line    $USER1$/check_http -I $HOSTADDRESS$ $ARG1$ -t 30
        }
define command{
        command_name    check_http_auth
        command_line    $USER1$/check_http -I $HOSTADDRESS$ $ARG1$ -a nagios:PaSsword#
        }

define command{
	command_name	check_https
	command_line	$USER1$/check_http -I  $HOSTADDRESS$ -S $ARG1$
}
define command{
	command_name    check_https_auth
        command_line    $USER1$/check_http -I  $HOSTADDRESS$ -S $ARG1$ -a nagios:PaSsword#
}
define command{
	command_name	check_vhost
	command_line	$USER1$/check_http -H $ARG1$ -f follow -u $ARG2$ -s $ARG3$ -t 30
}
define command{
	command_name	check_vhost_ssl
	command_line	$USER1$/check_http -H $ARG1$ -f follow -u $ARG2$ -s $ARG3$ -t 30 -S
}
define command{
	command_name	check_vhost_simple
	command_line	$USER1$/check_http -H $ARG1$
}

define command { 
command_name check_redis
command_line $USER1$/check_redis.pl -H $HOSTADDRESS$ -p $ARG1$ -T $ARG2$ -R -r -m -a $ARG3$ -w $ARG4$ -c $ARG5$ -f
} 

# 'check_redis_responsetime'
define command { 
command_name check_redis_responsetimes
command_line $USER1$/check_redis.pl -H $HOSTADDRESS$ -p $ARG1$ -T $ARG2$
} 

# 'check_redis_memory'
define command { 
command_name check_redis_memory
command_line $USER1$/check_redis.pl -H $HOSTADDRESS$ -p $ARG1$ -m $ARG2$ -M $ARG3$
}

# 'check_ssh' command definition
define command{
	command_name	check_ssh
	command_line	$USER1$/check_ssh -t 30 $ARG1$ $HOSTADDRESS$
	}


# 'check_dhcp' command definition
define command{
	command_name	check_dhcp
	command_line	$USER1$/check_dhcp $ARG1$
	}


# 'check_ping' command definition
define command{
        command_name    check_ping
        command_line    $USER1$/check_ping -H $HOSTADDRESS$ -w $ARG1$ -c $ARG2$ -p 5
        }


# 'check_pop' command definition
define command{
        command_name    check_pop
        command_line    $USER1$/check_pop -H $HOSTADDRESS$ $ARG1$ -t 30
        }


# 'check_imap' command definition
define command{
        command_name    check_imap
        command_line    $USER1$/check_imap -H $HOSTADDRESS$ $ARG1$ -t 30
        }
# secure imap
define command{
	command_name	check_simap
	command_line	$USER1$/check_imap -H $HOSTADDRESS$ $ARG1$ -S -t 30
}

# 'check_smtp' command definition
define command{
        command_name    check_smtp
        command_line    $USER1$/check_smtp -H $HOSTADDRESS$ $ARG1$ -t 30
        }


# 'check_tcp' command definition
define command{
	command_name	check_tcp
	command_line	$USER1$/check_tcp -H $HOSTADDRESS$ -p $ARG1$ $ARG2$
	}


# 'check_udp' command definition
define command{
	command_name	check_udp
	command_line	$USER1$/check_udp -H $HOSTADDRESS$ -p $ARG1$ $ARG2$
	}


# 'check_nt' command definition
define command{
	command_name	check_nt
	command_line	$USER1$/check_nt -H $HOSTADDRESS$ -p 12489 -v $ARG1$ $ARG2$
	}

# 'check_ssh_lsof' command definition
#define command{
#	command_name	check_mysql_lsof
#	command_line	$USER1$/check_by_ssh -H $HOSTADDRESS$ -n $HOSTNAME$ -C '/usr/lib64/nagios/plugins/check_mysql_lsof'
#	}
######################################
#	REMOTE COMMANDS
######################################

define command{
	command_name	check_nrpe
	command_line	$USER1$/check_nrpe -2 -u -t 30 -H $HOSTADDRESS$ -c $ARG1$ -a $ARG2$ $ARG3$ $ARG4$ $ARG5$ $ARG6$ $ARG7$ $ARG8$
}

define command{
        command_name    check_nrpe_remote
        command_line    $USER1$/check_nrpe -u -t 30 -H $HOSTADDRESS$ -c $ARG1$
}

define command{
        command_name check_nrpe_mem
        command_line /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c $ARG1$ 
        }

# this command runs a program $ARG1$ with no arguments
define command {
        command_name    check_nrpe_1arg
        command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -u -H $HOSTADDRESS$ -c $ARG1$
}
define command{
	command_name	check_nrpe_status
	command_line 	/usr/lib64/nagios/plugins/check_nrpe -2 -t 30 -H $HOSTADDRESS$
}
define command {
        command_name    check_nrpe_time
        command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c $ARG1$ -t 120
}

define command {
        command_name    check_nrpe_minute
        command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c $ARG1$ -t 60
}

define command {
        command_name    check_nrpe_two
        command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c $ARG1$ -t 120 
}

define command{
	command_name	check_remote_mysql_latency
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_mysql_latency -a $ARG1$ $ARG2$ $ARG3$ $ARG4$
	#$ARG1 = mysql username, $ARG2$ = warning threshold, $ARG3$ = critical threshold 
}

define command{
	command_name	check_remote_mysql_latency_port
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_mysql_latency -a $ARG1$ $ARG2$ $ARG3$ $ARG4$
	#$ARG1 = mysql username, $ARG2$ = warning threshold, $ARG3$ = critical threshold , $ARG4$ = port
}

define command{
	command_name 	check_remote_mysql_slave
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_mysql_slave -a $ARG1$
	#$ARG1 = mysql username
}

define command{
	command_name	check_remote_disk
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -t 30 -c check_disk -a $ARG1$ $ARG2$ $ARG3$ 
	#$ARG1$ = warning threshold, $ARG2$ = critical threshold, $ARG3$ = path
}
define command{
	command_name	check_remote_load
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -t 30 -c check_load -a $ARG1$ $ARG2$
	#$ARG1$ = warning threshold, $ARG2$ = critical threshold
}

define command{
	command_name	check_remote_procs
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -t 30 -c check_procs -a $ARG1$ $ARG2$ $ARG3$
	# $ARG1$ = warning threshold, $ARG2$ = critical threshold, $ARG3$ = proc name
}
define command{
	command_name	check_remote_procs_by_string
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -t 30 -c check_procs_by_string -a $ARG1$
}

define command{
	command_name	check_queue_latency
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $ARG1$ -t 30 -c check_queue_latency -a $ARG2$ $ARG3$ $ARG4$ $ARG5$
}

define command{
	command_name	check_bunchball
	command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_bunchball	
}

define command{
	command_name	check_var_log
	command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_var_log	
}

define command{
	command_name	check_myswap
	command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_myswap	
}

define command{
	command_name	check_myload
	command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_myload	
}

define command{
	command_name	check_myinode
	command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_myinode	
}

#define command{
#        command_name    check_inode_root_home
#        command_line    /usr/lib64/nagios/plugins/check_inode_root_home.sh
#}

define command{
        command_name    check_nrpe_lun
        command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c $ARG1$
}

define command{
        command_name    check_naCPU
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v CPULOAD -w 50 -c 70
        }
define command{
        command_name    check_naDISK
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v FAILEDDISK
        }
define command{
        command_name    check_naFAN
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v FAN
        }
define command{
        command_name    check_naUPTIME
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v UPTIME
        }
define command{
        command_name    check_naPS
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v PS
        }
define command{
        command_name    check_naNVRAM
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v NVRAM 
        }
define command{
        command_name    check_naSPACE
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v PERUSED
        }
define command{
        command_name    check_tab_pet
        command_line    /usr/lib64/nagios/plugins/check_tab_pet
        }
define command{
        command_name    check_tab_sig
        command_line    /usr/lib64/nagios/plugins/check_tab_sig
        }
define command{
        command_name    check_ws_optins_queue
        command_line    /usr/lib64/nagios/plugins/check_ws_optins_queue
        }

################################################################################
#
# SAMPLE PERFORMANCE DATA COMMANDS
#
# These are sample performance data commands that can be used to send performance
# data output to two text files (one for hosts, another for services).  If you
# plan on simply writing performance data out to a file, consider using the 
# host_perfdata_file and service_perfdata_file options in the main config file.
#
################################################################################


# 'process-host-perfdata' command definition
define command{
	command_name	process-host-perfdata
	command_line	/usr/bin/printf "%b" "$LASTHOSTCHECK$\t$HOSTNAME$\t$HOSTSTATE$\t$HOSTATTEMPT$\t$HOSTSTATETYPE$\t$HOSTEXECUTIONTIME$\t$HOSTOUTPUT$\t$HOSTPERFDATA$\n" >> /usr/local/nagios/var/host-perfdata.out
	}


# 'process-service-perfdata' command definition
define command{
	command_name	process-service-perfdata
	command_line	/usr/bin/printf "%b" "$LASTSERVICECHECK$\t$HOSTNAME$\t$SERVICEDESC$\t$SERVICESTATE$\t$SERVICEATTEMPT$\t$SERVICESTATETYPE$\t$SERVICEEXECUTIONTIME$\t$SERVICELATENCY$\t$SERVICEOUTPUT$\t$SERVICEPERFDATA$\n" >> /usr/local/nagios/var/service-perfdata.out
	}

# begin nagiosgraph configuration
# command to process nagios performance data for nagiosgraph
define command {
  command_name process-service-perfdata-for-nagiosgraph
  command_line /usr/share/nagios3/libexec/insert.pl
}
# end nagiosgraph configuration


# Slack notifications

# 'notify-service-by-slack' command definition
define command {
       command_name     notify-service-by-slack
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#alerts -field HOSTALIAS="$HOSTNAME$" -field SERVICEDESC="$SERVICEDESC$" -field SERVICESTATE="$SERVICESTATE$" -field SERVICEOUTPUT="$SERVICEOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}

# 'notify-host-by-slack' command definition
define command {
       command_name     notify-host-by-slack
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#alerts -field HOSTALIAS="$HOSTNAME$" -field HOSTSTATE="$HOSTSTATE$" -field HOSTOUTPUT="$HOSTOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}

# alerts that go to eugene slack channel
define command {
       command_name     notify-service-by-slack-eugene
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#cron-eugene -field HOSTALIAS="$HOSTNAME$" -field SERVICEDESC="$SERVICEDESC$" -field SERVICESTATE="$SERVICESTATE$" -field SERVICEOUTPUT="$SERVICEOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}

define command {
       command_name     notify-host-by-slack-eugene
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#cron-eugene -field HOSTALIAS="$HOSTNAME$" -field HOSTSTATE="$HOSTSTATE$" -field HOSTOUTPUT="$HOSTOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}

# alerts that go to eng-qa slack channel
define command {
       command_name     notify-service-by-slack-qa
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#eng-qa -field HOSTALIAS="$HOSTNAME$" -field SERVICEDESC="$SERVICEDESC$" -field SERVICESTATE="$SERVICESTATE$" -field SERVICEOUTPUT="$SERVICEOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}

define command {
       command_name     notify-host-by-slack-qa
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#eng-qa -field HOSTALIAS="$HOSTNAME$" -field HOSTSTATE="$HOSTSTATE$" -field HOSTOUTPUT="$HOSTOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}


define command {
  command_name check_mysql_lsof
  command_line /usr/lib64/nagios/plugins/check_lsof_mysql.sh $HOSTNAME$
}

define command {
  command_name check_mysql_lsof_0210
  command_line /usr/lib64/nagios/plugins/check_lsof_mysql_02-10.sh $HOSTNAME$
}


define command {
  command_name mysql_rep_heartbeat
  command_line /usr/lib64/nagios/plugins/nagios-mysql-heartbeat.sh $HOSTNAME$
}
define command {
  command_name mysql_rep_heartbeat_backends
  command_line /usr/lib64/nagios/plugins/nagios-mysql-heartbeat_backends.sh $HOSTNAME$
}

# check jenkins job plugin
# https://github.com/jonlives/nagios-jenkins-plugin

define command {
  command_name    check_jenkins_cron
  command_line    /usr/bin/perl /usr/lib64/nagios/plugins/check_jenkins_cron.pl -j $ARG1$ -l $ARG2$ -w $ARG3$ -c $ARG4$ 
}

define command {
  command_name    check_jenkins_job_ext
  command_line    /usr/bin/perl /usr/lib64/nagios/plugins/check_jenkins_job_extended.pl $ARG1$ $ARG2$ $ARG3$ $ARG4$ $ARG5$ $ARG6$ $ARG7$ $ARG8$
}

define command {
  command_name    restart_httpd
  command_line   /usr/bin/bash /usr/lib64/nagios/plugins/restart_httpd.sh $SERVICESTATE$ $SERVICESTATETYPE$ $SERVICEATTEMPT$
}

# Bash easy checks
define command{
	command_name	check_woff_font_cache
	command_line    /bin/bash /usr/lib64/nagios/plugins/check_woff_font_headers.sh 
}

define command{
	command_name	kick_ngms_node
	command_line    /usr/bin/sudo /bin/bash /usr/lib64/nagios/plugins/reset_enigma_node.sh 
}

# Python Plugins
define command{
	command_name	clear_swap_webservers
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/clear_swap_webservers.py $HOSTADDRESS$
}
define command{
	command_name	clear_mem
	command_line    sudo /bin/bash /usr/lib64/nagios/plugins/clear_mem.sh $HOSTADDRESS$
}

define command{
	command_name	find_empty_images
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/find_empty_images.py
}
define command{
	command_name	check_cache_control
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/check_cache_control.py
}
define command{
	command_name	ntp_update
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/ntp_update.py $HOSTADDRESS$
}
define command{
	command_name	ngms_kick
	command_line    sudo /bin/bash /usr/lib64/nagios/plugins/kick_ngms.sh $SERVICESTATE$ $SERVICESTATETYPE$ $SERVICEATTEMPT$ $HOSTNAME$ $SERVICEOUTPUT$
}
define command{
	command_name	email_activity_table_check
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/check_email_tableUpdate.py
}
define command{
	command_name	check_bb_queue
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/check_bb_queue.py
}
define command{
	command_name	flush_tables_3306
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/flush_tables.py $HOSTNAME$ 3306
}
define command{
	command_name	flush_tables_3307
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/flush_tables.py $HOSTNAME$ 3307
}

# check jenkins slaves
define command{
	command_name	check_jenkins_slaves
	command_line    /bin/perl /usr/lib64/nagios/plugins/check_jenkins_slaves.pl $ARG1$ -c $ARG2$
}

[stevens@nagios care2-configs]$ sudo vi commands.cfg
[stevens@nagios care2-configs]$ sudo vi commands.cfg
[stevens@nagios care2-configs]$ cat commands.cfg
###############################################################################
# COMMANDS.CFG - SAMPLE COMMAND DEFINITIONS FOR NAGIOS 3.0.2
###############################################################################


################################################################################
# NOTIFICATION COMMANDS
################################################################################

# /usr/sbin/sendmail -f nagios@relay01.care2.com email@provider.com
# 'notify-host-by-email' command definition
define command{
	command_name	notify-host-by-email
	#command_line	/usr/bin/printf "%b ***** Nagios *****\n\nNotification Type: $NOTIFICATIONTYPE$\nHost: $HOSTNAME$\nState: $HOSTSTATE$\nAddress: $HOSTADDRESS$\nInfo: $HOSTOUTPUT$\n\nDate/Time: $LONGDATETIME$\n" | /bin/mail -s "** $NOTIFICATIONTYPE$ Host Alert: $HOSTNAME$ is $HOSTSTATE$ **" $CONTACTEMAIL$
	command_line	/usr/bin/printf "Subject: $HOSTSTATE$: $HOSTNAME$ is $HOSTSTATE$\n**Nagios $NOTIFICATIONTYPE$\nHost: $HOSTNAME$ is $HOSTSTATE$\nAddress:$HOSTADDRESS$\nInfo: $HOSTOUTPUT$\nTimestamp: $LONGDATETIME$\n" | /usr/sbin/sendmail -f nagios@care2team.com $CONTACTEMAIL$
	}

# 'notify-service-by-email' command definition
define command{
	command_name	notify-service-by-email
#	command_line	/usr/bin/printf "%b Nagios Notification Type: $NOTIFICATIONTYPE$\nService: $SERVICEDESC$ is $SERVICESTATE$\nHost: $HOSTALIAS$\nAddress: $HOSTADDRESS$\nDate/Time: $LONGDATETIME$\nAdditional Info:\n$SERVICEOUTPUT$\n\nURL: $SERVICENOTESURL$" | /bin/mail -s "** $NOTIFICATIONTYPE$ Service Alert: $HOSTALIAS$/$SERVICEDESC$ is $SERVICESTATE$ **" $CONTACTEMAIL$
	command_line 	/usr/bin/printf "Subject:$SERVICESTATE$: $SERVICEDESC$ on $HOSTALIAS$ is $SERVICESTATE$\n**Nagios** $NOTIFICATIONTYPE$\n$SERVICEDESC$ is $SERVICESTATE$\nHost: $HOSTALIAS$ ($HOSTADDRESS$)\nInfo:$SERVICEOUTPUT$\nNotes: $SERVICEACKCOMMENT$\nTimestamp: $LONGDATETIME$\n" | /usr/sbin/sendmail -f nagios@nagios.care2.com $CONTACTEMAIL$

	}

################################################################################
# HOST CHECK COMMANDS
################################################################################


# This command checks to see if a host is "alive" by pinging it
# The check must result in a 100% packet loss or 5 second (5000ms) round trip 
# average time to produce a critical error.
# Note: Five ICMP echo packets are sent (determined by the '-p 5' argument)

# 'check-host-alive' command definition
define command{
        command_name    PING
        command_line    $USER1$/check_ping -H $HOSTADDRESS$ -w 3000.0,80% -c 5000.0,100% -p 5
        }



################################################################################
# SERVICE CHECK COMMANDS
################################################################################

define command{
	command_name	check_crond
	command_line	$USER1$/check_ntp -H $HOSTADDRESS$ -c $ARG2$
}

define command{
	command_name	check_ntp
	command_line	$USER1$/check_ntp -H $HOSTADDRESS$ -w $ARG1$ -c $ARG2$
}

define command{
	command_name	check_mysql
	command_line	$USER1$/check_tcp -H $HOSTADDRESS$ -p $ARG1$ $ARG2$
}

define command{
	command_name	httpd_procs
	command_line	$USER1$/check_procs -w 10 -C httpd
}

# 'check_local_disk' command definition
define command{
        command_name    check_local_disk
        command_line    $USER1$/check_disk -w $ARG1$ -c $ARG2$ -p $ARG3$
        }


# 'check_local_load' command definition
define command{
        command_name    check_local_load
        command_line    $USER1$/check_load -w $ARG1$ -c $ARG2$
        }


# 'check_local_procs' command definition
define command{
        command_name    check_local_procs
        command_line    $USER1$/check_procs -w $ARG1$ -c $ARG2$ -s $ARG3$
        }


# 'check_local_users' command definition
define command{
        command_name    check_local_users
        command_line    $USER1$/check_users -w $ARG1$ -c $ARG2$
        }


# 'check_local_swap' command definition
define command{
	command_name	check_local_swap
	command_line	$USER1$/check_swap -w $ARG1$ -c $ARG2$
	}


# 'check_local_mrtgtraf' command definition
define command{
	command_name	check_local_mrtgtraf
	command_line	$USER1$/check_mrtgtraf -F $ARG1$ -a $ARG2$ -w $ARG3$ -c $ARG4$ -e $ARG5$
	}

define command{
	command_name	check_dns
	command_line	$USER1$/check_dns -H $ARG1$ -s $HOSTADDRESS$ -a $ARG2$ -t $ARG3$
}

define command{
	command_name	check_ldap
	command_line	$USER1$/check_ldap -H $HOSTADDRESS$ -a $ARG1$ -b $ARG2$ -3 -w $ARG4$ -c $ARG5$ -t 30
}
################################################################################
# NOTE:  The following 'check_...' commands are used to monitor services on
#        both local and remote hosts.
################################################################################


# 'check_ftp' command definition
define command{
        command_name    check_ftp
        command_line    $USER1$/check_ftp -H $HOSTADDRESS$ $ARG1$
        }


# 'check_hpjd' command definition
define command{
        command_name    check_hpjd
        command_line    $USER1$/check_hpjd -H $HOSTADDRESS$ $ARG1$
        }


# 'check_snmp' command definition
define command{
        command_name    check_snmp
        command_line    $USER1$/check_snmp -H $HOSTADDRESS$ $ARG1$
        }

# Command definition for Network Technologies Inc SNMP plugin
 define command{
       command_name    check_nti_snmp
       command_line    $USER1$/check_nti_snmp.pl -H $HOSTADDRESS$ $ARG1$
       }

# 'check_http' command definition
define command{
        command_name    check_http
        command_line    $USER1$/check_http -I $HOSTADDRESS$ $ARG1$ -t 30
        }
define command{
        command_name    check_http_auth
        command_line    $USER1$/check_http -I $HOSTADDRESS$ $ARG1$ -a nagios:PaSsword#
        }

define command{
	command_name	check_https
	command_line	$USER1$/check_http -I  $HOSTADDRESS$ -S $ARG1$
}
define command{
	command_name    check_https_auth
        command_line    $USER1$/check_http -I  $HOSTADDRESS$ -S $ARG1$ -a nagios:PaSsword#
}
define command{
	command_name	check_vhost
	command_line	$USER1$/check_http -H $ARG1$ -f follow -u $ARG2$ -s $ARG3$ -t 30
}
define command{
	command_name	check_vhost_ssl
	command_line	$USER1$/check_http -H $ARG1$ -f follow -u $ARG2$ -s $ARG3$ -t 30 -S
}
define command{
	command_name	check_vhost_simple
	command_line	$USER1$/check_http -H $ARG1$
}

define command { 
command_name check_redis
command_line $USER1$/check_redis.pl -H $HOSTADDRESS$ -p $ARG1$ -T $ARG2$ -R -r -m -a $ARG3$ -w $ARG4$ -c $ARG5$ -f
} 

# 'check_redis_responsetime'
define command { 
command_name check_redis_responsetimes
command_line $USER1$/check_redis.pl -H $HOSTADDRESS$ -p $ARG1$ -T $ARG2$
} 

# 'check_redis_memory'
define command { 
command_name check_redis_memory
command_line $USER1$/check_redis.pl -H $HOSTADDRESS$ -p $ARG1$ -m $ARG2$ -M $ARG3$
}

# 'check_ssh' command definition
define command{
	command_name	check_ssh
	command_line	$USER1$/check_ssh -t 30 $ARG1$ $HOSTADDRESS$
	}


# 'check_dhcp' command definition
define command{
	command_name	check_dhcp
	command_line	$USER1$/check_dhcp $ARG1$
	}


# 'check_ping' command definition
define command{
        command_name    check_ping
        command_line    $USER1$/check_ping -H $HOSTADDRESS$ -w $ARG1$ -c $ARG2$ -p 5
        }


# 'check_pop' command definition
define command{
        command_name    check_pop
        command_line    $USER1$/check_pop -H $HOSTADDRESS$ $ARG1$ -t 30
        }


# 'check_imap' command definition
define command{
        command_name    check_imap
        command_line    $USER1$/check_imap -H $HOSTADDRESS$ $ARG1$ -t 30
        }
# secure imap
define command{
	command_name	check_simap
	command_line	$USER1$/check_imap -H $HOSTADDRESS$ $ARG1$ -S -t 30
}

# 'check_smtp' command definition
define command{
        command_name    check_smtp
        command_line    $USER1$/check_smtp -H $HOSTADDRESS$ $ARG1$ -t 30
        }


# 'check_tcp' command definition
define command{
	command_name	check_tcp
	command_line	$USER1$/check_tcp -H $HOSTADDRESS$ -p $ARG1$ $ARG2$
	}


# 'check_udp' command definition
define command{
	command_name	check_udp
	command_line	$USER1$/check_udp -H $HOSTADDRESS$ -p $ARG1$ $ARG2$
	}


# 'check_nt' command definition
define command{
	command_name	check_nt
	command_line	$USER1$/check_nt -H $HOSTADDRESS$ -p 12489 -v $ARG1$ $ARG2$
	}

# 'check_ssh_lsof' command definition
#define command{
#	command_name	check_mysql_lsof
#	command_line	$USER1$/check_by_ssh -H $HOSTADDRESS$ -n $HOSTNAME$ -C '/usr/lib64/nagios/plugins/check_mysql_lsof'
#	}
######################################
#	REMOTE COMMANDS
######################################

define command{
	command_name	check_nrpe
	command_line	$USER1$/check_nrpe -2 -u -t 30 -H $HOSTADDRESS$ -c $ARG1$ -a $ARG2$ $ARG3$ $ARG4$ $ARG5$ $ARG6$ $ARG7$ $ARG8$
}

define command{
        command_name    check_nrpe_remote
        command_line    $USER1$/check_nrpe -u -t 30 -H $HOSTADDRESS$ -c $ARG1$
}

define command{
        command_name check_nrpe_mem
        command_line /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c $ARG1$ 
        }

# this command runs a program $ARG1$ with no arguments
define command {
        command_name    check_nrpe_1arg
        command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -u -H $HOSTADDRESS$ -c $ARG1$
}
define command{
	command_name	check_nrpe_status
	command_line 	/usr/lib64/nagios/plugins/check_nrpe -2 -t 30 -H $HOSTADDRESS$
}
define command {
        command_name    check_nrpe_time
        command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c $ARG1$ -t 120
}

define command {
        command_name    check_nrpe_minute
        command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c $ARG1$ -t 60
}

define command {
        command_name    check_nrpe_two
        command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c $ARG1$ -t 120 
}

define command{
	command_name	check_remote_mysql_latency
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_mysql_latency -a $ARG1$ $ARG2$ $ARG3$ $ARG4$
	#$ARG1 = mysql username, $ARG2$ = warning threshold, $ARG3$ = critical threshold 
}

define command{
	command_name	check_remote_mysql_latency_port
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_mysql_latency -a $ARG1$ $ARG2$ $ARG3$ $ARG4$
	#$ARG1 = mysql username, $ARG2$ = warning threshold, $ARG3$ = critical threshold , $ARG4$ = port
}

define command{
	command_name 	check_remote_mysql_slave
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_mysql_slave -a $ARG1$
	#$ARG1 = mysql username
}

define command{
	command_name	check_remote_disk
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -t 30 -c check_disk -a $ARG1$ $ARG2$ $ARG3$ 
	#$ARG1$ = warning threshold, $ARG2$ = critical threshold, $ARG3$ = path
}
define command{
	command_name	check_remote_load
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -t 30 -c check_load -a $ARG1$ $ARG2$
	#$ARG1$ = warning threshold, $ARG2$ = critical threshold
}

define command{
	command_name	check_remote_procs
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -t 30 -c check_procs -a $ARG1$ $ARG2$ $ARG3$
	# $ARG1$ = warning threshold, $ARG2$ = critical threshold, $ARG3$ = proc name
}
define command{
	command_name	check_remote_procs_by_string
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -t 30 -c check_procs_by_string -a $ARG1$
}

define command{
	command_name	check_queue_latency
	command_line	/usr/lib64/nagios/plugins/check_nrpe -2 -H $ARG1$ -t 30 -c check_queue_latency -a $ARG2$ $ARG3$ $ARG4$ $ARG5$
}

define command{
	command_name	check_bunchball
	command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_bunchball	
}

define command{
	command_name	check_var_log
	command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_var_log	
}

define command{
	command_name	check_myswap
	command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_myswap	
}

define command{
	command_name	check_myload
	command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_myload	
}

define command{
	command_name	check_myinode
	command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c check_myinode	
}

#define command{
#        command_name    check_inode_root_home
#        command_line    /usr/lib64/nagios/plugins/check_inode_root_home.sh
#}

define command{
        command_name    check_nrpe_lun
        command_line    /usr/lib64/nagios/plugins/check_nrpe -2 -H $HOSTADDRESS$ -c $ARG1$
}

define command{
        command_name    check_naCPU
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v CPULOAD -w 50 -c 70
        }
define command{
        command_name    check_naDISK
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v FAILEDDISK
        }
define command{
        command_name    check_naFAN
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v FAN
        }
define command{
        command_name    check_naUPTIME
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v UPTIME
        }
define command{
        command_name    check_naPS
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v PS
        }
define command{
        command_name    check_naNVRAM
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v NVRAM 
        }
define command{
        command_name    check_naSPACE
        command_line    /usr/lib64/nagios/plugins/check_netapp -H $HOSTNAME$ -v PERUSED
        }
define command{
        command_name    check_tab_pet
        command_line    /usr/lib64/nagios/plugins/check_tab_pet
        }
define command{
        command_name    check_tab_sig
        command_line    /usr/lib64/nagios/plugins/check_tab_sig
        }
define command{
        command_name    check_ws_optins_queue
        command_line    /usr/lib64/nagios/plugins/check_ws_optins_queue
        }

################################################################################
#
# SAMPLE PERFORMANCE DATA COMMANDS
#
# These are sample performance data commands that can be used to send performance
# data output to two text files (one for hosts, another for services).  If you
# plan on simply writing performance data out to a file, consider using the 
# host_perfdata_file and service_perfdata_file options in the main config file.
#
################################################################################


# 'process-host-perfdata' command definition
define command{
	command_name	process-host-perfdata
	command_line	/usr/bin/printf "%b" "$LASTHOSTCHECK$\t$HOSTNAME$\t$HOSTSTATE$\t$HOSTATTEMPT$\t$HOSTSTATETYPE$\t$HOSTEXECUTIONTIME$\t$HOSTOUTPUT$\t$HOSTPERFDATA$\n" >> /usr/local/nagios/var/host-perfdata.out
	}


# 'process-service-perfdata' command definition
define command{
	command_name	process-service-perfdata
	command_line	/usr/bin/printf "%b" "$LASTSERVICECHECK$\t$HOSTNAME$\t$SERVICEDESC$\t$SERVICESTATE$\t$SERVICEATTEMPT$\t$SERVICESTATETYPE$\t$SERVICEEXECUTIONTIME$\t$SERVICELATENCY$\t$SERVICEOUTPUT$\t$SERVICEPERFDATA$\n" >> /usr/local/nagios/var/service-perfdata.out
	}

# begin nagiosgraph configuration
# command to process nagios performance data for nagiosgraph
define command {
  command_name process-service-perfdata-for-nagiosgraph
  command_line /usr/share/nagios3/libexec/insert.pl
}
# end nagiosgraph configuration


# Slack notifications

# 'notify-service-by-slack' command definition
define command {
       command_name     notify-service-by-slack
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#alerts -field HOSTALIAS="$HOSTNAME$" -field SERVICEDESC="$SERVICEDESC$" -field SERVICESTATE="$SERVICESTATE$" -field SERVICEOUTPUT="$SERVICEOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}

# 'notify-host-by-slack' command definition
define command {
       command_name     notify-host-by-slack
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#alerts -field HOSTALIAS="$HOSTNAME$" -field HOSTSTATE="$HOSTSTATE$" -field HOSTOUTPUT="$HOSTOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}

# alerts that go to eugene slack channel
define command {
       command_name     notify-service-by-slack-eugene
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#cron-eugene -field HOSTALIAS="$HOSTNAME$" -field SERVICEDESC="$SERVICEDESC$" -field SERVICESTATE="$SERVICESTATE$" -field SERVICEOUTPUT="$SERVICEOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}

define command {
       command_name     notify-host-by-slack-eugene
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#cron-eugene -field HOSTALIAS="$HOSTNAME$" -field HOSTSTATE="$HOSTSTATE$" -field HOSTOUTPUT="$HOSTOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}

# alerts that go to eng-qa slack channel
define command {
       command_name     notify-service-by-slack-qa
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#eng-qa -field HOSTALIAS="$HOSTNAME$" -field SERVICEDESC="$SERVICEDESC$" -field SERVICESTATE="$SERVICESTATE$" -field SERVICEOUTPUT="$SERVICEOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}

define command {
       command_name     notify-host-by-slack-qa
       command_line      /usr/local/bin/slack_nagios.pl -field slack_channel=#eng-qa -field HOSTALIAS="$HOSTNAME$" -field HOSTSTATE="$HOSTSTATE$" -field HOSTOUTPUT="$HOSTOUTPUT$" -field NOTIFICATIONTYPE="$NOTIFICATIONTYPE$"
}


define command {
  command_name check_mysql_lsof
  command_line /usr/lib64/nagios/plugins/check_lsof_mysql.sh $HOSTNAME$
}

define command {
  command_name check_mysql_lsof_0210
  command_line /usr/lib64/nagios/plugins/check_lsof_mysql_02-10.sh $HOSTNAME$
}


define command {
  command_name mysql_rep_heartbeat
  command_line /usr/lib64/nagios/plugins/nagios-mysql-heartbeat.sh $HOSTNAME$
}
define command {
  command_name mysql_rep_heartbeat_backends
  command_line /usr/lib64/nagios/plugins/nagios-mysql-heartbeat_backends.sh $HOSTNAME$
}

# check jenkins job plugin
# https://github.com/jonlives/nagios-jenkins-plugin

define command {
  command_name    check_jenkins_cron
  command_line    /usr/bin/perl /usr/lib64/nagios/plugins/check_jenkins_cron.pl -j $ARG1$ -l $ARG2$ -w $ARG3$ -c $ARG4$ 
}

define command {
  command_name    check_jenkins_job_ext
  command_line    /usr/bin/perl /usr/lib64/nagios/plugins/check_jenkins_job_extended.pl $ARG1$ $ARG2$ $ARG3$ $ARG4$ $ARG5$ $ARG6$ $ARG7$ $ARG8$
}

define command {
  command_name    restart_httpd
  command_line   /usr/bin/bash /usr/lib64/nagios/plugins/restart_httpd.sh $SERVICESTATE$ $SERVICESTATETYPE$ $SERVICEATTEMPT$
}

# Bash easy checks
define command{
	command_name	check_woff_font_cache
	command_line    /bin/bash /usr/lib64/nagios/plugins/check_woff_font_headers.sh 
}

define command{
	command_name	kick_ngms_node
	command_line    /usr/bin/sudo /bin/bash /usr/lib64/nagios/plugins/reset_enigma_node.sh 
}

# Python Plugins
define command{
	command_name	clear_swap_webservers
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/clear_swap_webservers.py $HOSTADDRESS$
}
define command{
	command_name	clear_mem
	command_line    sudo /bin/bash /usr/lib64/nagios/plugins/clear_mem.sh $HOSTADDRESS$
}

define command{
	command_name	find_empty_images
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/find_empty_images.py
}
define command{
	command_name	check_cache_control
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/check_cache_control.py
}
define command{
	command_name	ntp_update
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/ntp_update.py $HOSTADDRESS$
}
define command{
	command_name	ngms_kick
	command_line    sudo /bin/bash /usr/lib64/nagios/plugins/kick_ngms.sh $SERVICESTATE$ $SERVICESTATETYPE$ $SERVICEATTEMPT$ $HOSTNAME$ $SERVICEOUTPUT$
}
define command{
	command_name	email_activity_table_check
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/check_email_tableUpdate.py
}
define command{
	command_name	check_bb_queue
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/check_bb_queue.py
}
define command{
	command_name	flush_tables_3306
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/flush_tables.py $HOSTNAME$ 3306
}
define command{
	command_name	flush_tables_3307
	command_line    /usr/bin/python /usr/lib64/nagios/plugins/flush_tables.py $HOSTNAME$ 3307
}

# check jenkins slaves
define command{
	command_name	check_jenkins_slaves
	command_line    /bin/perl /usr/lib64/nagios/plugins/check_jenkins_slaves.pl $ARG1$ -c $ARG2$
}

