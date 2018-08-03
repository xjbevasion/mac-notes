
Socket file operations

```
const struct fileops socketops = {
	.fo_type = DTYPE_SOCKET,
	.fo_read = soo_read,
	.fo_write = soo_write,
	.fo_ioctl = soo_ioctl,
	.fo_select = soo_select,
	.fo_close = soo_close,
	.fo_kqfilter = soo_kqfilter,
	.fo_drain = soo_drain,
};
```

AF_UNIX socket connection and structure

```
(lldb) bt
* thread #7, name = '0xffffff80193b0080', queue = '0x0', stop reason = breakpoint 1.1
  * frame #0: 0xffffff8006af26f8 kernel.development`unp_connect(so=<unavailable>, nam=<unavailable>, p=<unavailable>) at uipc_usrreq.c:1104 [opt]
    frame #1: 0xffffff8006adc83d kernel.development`soconnectlock(so=0xffffff801671dee0, nam=<unavailable>, dolock=<unavailable>) at uipc_socket.c:1671 [opt]
    frame #2: 0xffffff8006aeca53 kernel.development`connect_nocancel [inlined] connectit(so=<unavailable>, sa=<unavailable>) at uipc_syscalls.c:926 [opt]
    frame #3: 0xffffff8006aec9f2 kernel.development`connect_nocancel(p=<unavailable>, uap=<unavailable>, retval=<unavailable>) at uipc_syscalls.c:722 [opt]
    frame #4: 0xffffff8006ba69fa kernel.development`unix_syscall64(state=<unavailable>) at systemcalls.c:382 [opt]
    frame #5: 0xffffff80065229d6 kernel.development`hndl_unix_scall64 + 22
    
int
soconnectlock(struct socket *so, struct sockaddr *nam, int dolock)
{
...
			error = (*so->so_proto->pr_usrreqs->pru_connect)
			    (so, nam, p);
...
}

(lldb) p *so->so_proto
(protosw) $39 = {
  pr_entry = {
    tqe_next = 0xffffff8006ea11a8
    tqe_prev = 0xffffff8006ea10c0
  }
  pr_domain = 0xffffff8006ea1088
  pr_protosw = 0xffffff8006ea1110
  pr_type = 1
  pr_protocol = 0
  pr_flags = 3356
  pr_input = 0x0000000000000000
  pr_output = 0x0000000000000000
  pr_ctlinput = 0x0000000000000000
  pr_ctloutput = 0xffffff8006af4170 (kernel.development`uipc_ctloutput at uipc_usrreq.c:709)
  pr_usrreqs = 0xffffff8006ea2130
  pr_init = 0x0000000000000000
  pr_drain = 0x0000000000000000
  pr_sysctl = 0x0000000000000000
  pr_lock = 0xffffff8006af4830 (kernel.development`unp_lock at uipc_usrreq.c:2429)
  pr_unlock = 0xffffff8006af4920 (kernel.development`unp_unlock at uipc_usrreq.c:2457)
  pr_getlock = 0xffffff8006af4e40 (kernel.development`unp_getlock at uipc_usrreq.c:2501)
  pr_filter_head = {
    tqh_first = 0x0000000000000000
    tqh_last = 0xffffff8006ea1190
  }
  pr_old = 0x0000000000000000
}
(lldb) p *so->so_proto->pr_usrreqs
(pr_usrreqs) $40 = {
  pru_flags = 0
  pru_abort = 0xffffff8006af1490 (kernel.development`uipc_abort at uipc_usrreq.c:207)
  pru_accept = 0xffffff8006af14e0 (kernel.development`uipc_accept at uipc_usrreq.c:220)
  pru_attach = 0xffffff8006af1530 (kernel.development`uipc_attach at uipc_usrreq.c:247)
  pru_bind = 0xffffff8006af1690 (kernel.development`uipc_bind at uipc_usrreq.c:257)
  pru_connect = 0xffffff8006af1a40 (kernel.development`uipc_connect at uipc_usrreq.c:273)
  pru_connect2 = 0xffffff8006af1a60 (kernel.development`uipc_connect2 at uipc_usrreq.c:289)
  pru_connectx = 0xffffff8006aeadf0 (kernel.development`pru_connectx_notsupp at uipc_socket2.c:2110)
  pru_control = 0xffffff8006aeae00 (kernel.development`pru_control_notsupp at uipc_socket2.c:2118)
  pru_detach = 0xffffff8006af1a80 (kernel.development`uipc_detach at uipc_usrreq.c:302)
  pru_disconnect = 0xffffff8006af1ac0 (kernel.development`uipc_disconnect at uipc_usrreq.c:316)
  pru_disconnectx = 0xffffff8006aeae30 (kernel.development`pru_disconnectx_notsupp at uipc_socket2.c:2139)
  pru_listen = 0xffffff8006af1ae0 (kernel.development`uipc_listen at uipc_usrreq.c:330)
  pru_peeraddr = 0xffffff8006af1b90 (kernel.development`uipc_peeraddr at uipc_usrreq.c:340)
  pru_rcvd = 0xffffff8006af1be0 (kernel.development`uipc_rcvd at uipc_usrreq.c:356)
  pru_rcvoob = 0xffffff8006aeae70 (kernel.development`pru_rcvoob_notsupp at uipc_socket2.c:2167)
  pru_send = 0xffffff8006af1cc0 (kernel.development`uipc_send at uipc_usrreq.c:421)
  pru_send_list = 0xffffff8006aeae90 (kernel.development`pru_send_list_notsupp at uipc_socket2.c:2183)
  pru_sense = 0xffffff8006af2510 (kernel.development`uipc_sense at uipc_usrreq.c:612)
  pru_shutdown = 0xffffff8006af2590 (kernel.development`uipc_shutdown at uipc_usrreq.c:658)
  pru_sockaddr = 0xffffff8006af2600 (kernel.development`uipc_sockaddr at uipc_usrreq.c:674)
  pru_sopoll = 0xffffff8006aeaf20 (kernel.development`pru_sopoll_notsupp at uipc_socket2.c:2259)
  pru_soreceive = 0xffffff8006ade6e0 (kernel.development`soreceive at uipc_socket.c:3095)
  pru_soreceive_list = 0xffffff8006aeaef0 (kernel.development`pru_soreceive_list_notsupp at uipc_socket2.c:2238)
  pru_sosend = 0xffffff8006add090 (kernel.development`sosend at uipc_socket.c:2040)
  pru_sosend_list = 0xffffff8006aeaed0 (kernel.development`pru_sosend_list_notsupp at uipc_socket2.c:2222)
  pru_socheckopt = 0xffffff8006aeaf30 (kernel.development`pru_socheckopt_null at uipc_socket2.c:2266)
  pru_preconnect = 0xffffff8006aeb180 (kernel.development`pru_preconnect_null at uipc_socket2.c:2276)
}
```