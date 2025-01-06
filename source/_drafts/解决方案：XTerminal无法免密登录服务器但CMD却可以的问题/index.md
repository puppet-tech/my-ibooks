
- Xterminal 2.9.3
- 密钥类型为 RSA 4096

这阵子遇到一个问题：在一台 Windows 系统上，我对一台远程的 Linux 服务器配置了免密登录，配置完成后，我通过 Windows 的 cmd 采用 ssh username@host 进行连接，验证免密登录成功，但是当我使用 XTerminal 进行连接，却提示以下报错：

```log
Local ident: 'SSH-2.0-ssh2js1.16.0'
Client: Trying 96.18.6.201 on port 22 ...
Socket connected
Remote ident: 'SSH-2.0-OpenSSH_8.2'
Outbound: Sending KEXINIT
Inbound: Handshake in progress
Handshake: (local) KEX method: curve25519-sha256@libssh.org,curve25519-sha256,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group14-sha256,diffie-hellman-group15-sha512,diffie-hellman-group16-sha512,diffie-hellman-group17-sha512,diffie-hellman-group18-sha512,ext-info-c,kex-strict-c-v00@openssh.com
Handshake: (remote) KEX method: curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group14-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1
Handshake: KEX algorithm: curve25519-sha256@libssh.org
Handshake: (local) Host key format: ssh-rsa
Handshake: (remote) Host key format: rsa-sha2-512,rsa-sha2-256,ssh-rsa,ecdsa-sha2-nistp256,ssh-ed25519
Handshake: Host key format: ssh-rsa
Handshake: (local) C->S cipher: aes128-gcm@openssh.com,aes256-gcm@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr
Handshake: (remote) C->S cipher: aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes256-ctr,aes256-cbc,aes128-gcm@openssh.com,aes128-ctr,aes128-cbc
Handshake: C->S Cipher: aes128-gcm@openssh.com
Handshake: (local) S->C cipher: aes128-gcm@openssh.com,aes256-gcm@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr
Handshake: (remote) S->C cipher: aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes256-ctr,aes256-cbc,aes128-gcm@openssh.com,aes128-ctr,aes128-cbc
Handshake: S->C cipher: aes128-gcm@openssh.com
Handshake: (local) C->S MAC: hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1
Handshake: (remote) C->S MAC: hmac-sha2-256-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha2-256,hmac-sha1,umac-128@openssh.com,hmac-sha2-512
Handshake: C->S MAC: <implicit>
Handshake: (local) S->C MAC: hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1
Handshake: (remote) S->C MAC: hmac-sha2-256-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha2-256,hmac-sha1,umac-128@openssh.com,hmac-sha2-512
Handshake: S->C MAC: <implicit>
Handshake: (local) C->S compression: none,zlib@openssh.com,zlib
Handshake: (remote) C->S compression: none,zlib@openssh.com
Handshake: C->S compression: none
Handshake: (local) S->C compression: none,zlib@openssh.com,zlib
Handshake: (remote) S->C compression: none,zlib@openssh.com
Handshake: S->C compression: none
Outbound: Sending KEXECDH_INIT
Received DH Reply
Host accepted by default (no verification)
Host accepted (verified)
Outbound: Sending NEWKEYS
Inbound: NEWKEYS
Verifying signature ...
Verified signature
Handshake completed
Outbound: Sending SERVICE_REQUEST (ssh-userauth)
Inbound: Received EXT_INFO
Inbound: Received SERVICE_ACCEPT (ssh-userauth)
Outbound: Sending USERAUTH_REQUEST (none)
Inbound: Received USERAUTH_BANNER
Inbound: Received USERAUTH_FAILURE (publickey,gssapi-keyex,gssapi-with-mic,password)
Client: none auth failed
Outbound: Sending USERAUTH_REQUEST (publickey -- check)
Inbound: Received USERAUTH_FAILURE (publickey,gssapi-keyex,gssapi-with-mic,password)
Client: publickey (rsa-sha2-256) auth failed
Client: publickey auth failed
Outbound: Sending USERAUTH_REQUEST (keyboard-interactive)
Inbound: Received USERAUTH_FAILURE (publickey,gssapi-keyex,gssapi-with-mic,password)
Client: keyboard-interactive auth failed
All configured authentication methods failed
```

同时，查看服务器的 /var/log/auth.log 日志，发现以下报错：

```log
Jan  6 14:27:02 cce-gd-scot-38913 sshd[20956]: reprocess config line 144: Deprecated option RSAAuthentication
Jan  6 14:27:02 cce-gd-scot-38913 sshd[20956]: reprocess config line 146: Deprecated option RhostsRSAAuthentication
Jan  6 14:27:02 cce-gd-scot-38913 sshd[20956]: Invalid user paas  from 10.16.75.136 port 55823
Jan  6 14:27:02 cce-gd-scot-38913 audit[20956]: CRYPTO_KEY_USER pid=20956 uid=0 auid=4294967295 ses=4294967295 msg='op=destroy kind=session fp=? direction=both spid=20957 suid=74 rport=55823 laddr=96.18.6.201 lport=22  exe="/usr/sbin/sshd
" hostname=? addr=10.16.75.136 terminal=? res=success'
Jan  6 14:27:02 cce-gd-scot-38913 sshd[20956]: Received disconnect from 10.16.75.136 port 55823:11:  [preauth]
Jan  6 14:27:02 cce-gd-scot-38913 sshd[20956]: Disconnected from invalid user paas  10.16.75.136 port 55823 [preauth]
```

最终根据日志分析，问题主要出在用户名验证环节，用户名不正确，然后再回到 XTerminal 的密钥凭证再度进行检查，结果发现，真的有个空格！！！
