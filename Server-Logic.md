```
server_tunnel_initialize
    socket_read(incoming); // tunnel_stage_initial
    
do_init_package(tunnel, incoming);    
    tunnel_cipher_server_decrypt(cipher, buf, &receipt, &confirm);
    buffer_replace(init_pkg, result);
    if (receipt) {
        socket_write(incoming, receipt);  // tunnel_stage_receipt_done
        socket_read(incoming); // tunnel_stage_client_feedback;
        do_client_feedback(tunnel, incoming);
    } else if (confirm) {
        socket_write(incoming, confirm);  // tunnel_stage_confirm_done
        do_prepare_parse(tunnel, incoming);
    } else {
        do_prepare_parse(tunnel, incoming);
    }

do_client_feedback(tunnel, incoming);
    tunnel_cipher_server_decrypt(cipher, buf, NULL, &confirm);
    buffer_concatenate2(init_pkg, result);
    if (confirm) {
        socket_write(incoming, confirm); // tunnel_stage_confirm_done;
        do_prepare_parse(tunnel, incoming);
    } else {
        do_prepare_parse(tunnel, incoming);
    }

do_prepare_parse(tunnel, incoming);
    pre_parse_header(init_pkg);
    do_parse(tunnel, incoming);

do_parse(tunnel, incoming);
    if (ipFound == false) {
        socket_getaddrinfo(outgoing, host); // tunnel_stage_resolve_host
        do_resolve_host_done(tunnel, socket);
    } else {
        do_connect_host_start(tunnel, socket);
    }

do_resolve_host_done(tunnel, socket);
    do_connect_host_start(tunnel, socket);

do_connect_host_start(tunnel, socket);
    socket_connect(outgoing); // tunnel_stage_connect_host

do_connect_host_done(tunnel, socket);
    if (init_pkg->len > 0) {
        socket_write(outgoing, init_pkg); // tunnel_stage_launch_streaming;
        do_launch_streaming(tunnel, socket);
    } else {
        do_launch_streaming(tunnel, socket);
    }

do_launch_streaming(tunnel, socket);
    socket_read(incoming); // tunnel_stage_streaming
    socket_read(outgoing); // tunnel_stage_streaming

tunnel_traditional_streaming(tunnel, socket);
```
