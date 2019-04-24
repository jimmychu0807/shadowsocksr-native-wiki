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
```
