<!SLIDE subsection>
# STDIN metrics client
## [arbitrary data is arbitrary]

<!SLIDE >
* lightweight wrapper for local sensu socket

* expects graphite format metrics:
  * format_type VALUE TIMESTAMP\n

<!SLIDE code smaller>
# really simple

    @@@ sh
    echo "test.metrics.script 100 $(date +%s)" | stdin-metrics-client.rb

<!SLIDE code smaller>
# still simple

    @@@ Ruby
    Open3.popen3("stdin-metrics-client.rb") {|stdin,stdout,stderr,wait_thr|
      stdin.write(metrics.join('\n')
      pid = wait_thr.pid # pid of the started process
      exit_status = wait_thr.value # Process::Status object returned
    }

