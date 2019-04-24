```
client_tunnel_initialize
    socket_read(incoming); // tunnel_stage_handshake
do_handshake(tunnel);
    socket_write(incoming, "\5\0", 2); // tunnel_stage_handshake_replied
do_wait_s5_request(tunnel);
    socket_read(incoming); // tunnel_stage_s5_request    
do_parse_s5_request(tunnel);    
    ctx->init_pkg = initial_package_create(parser);
    ctx->cipher = tunnel_cipher_create(ctx->env, 1452);
    ctx->cipher->protocol;
    ctx->cipher->obfs;

    socket_getaddrinfo(outgoing, config->remote_host); // tunnel_stage_resolve_ssr_server_host_done

        do_resolve_ssr_server_host_aftercare(tunnel);

do_connect_ssr_server(tunnel);
    socket_connect(outgoing);   // tunnel_stage_connecting_ssr_server

do_connect_ssr_server_done(tunnel);
    tunnel_cipher_client_encrypt(ctx->init_pkg)
    socket_write(outgoing, ctx->init_pkg); // tunnel_stage_ssr_auth_sent
    
do_ssr_auth_sent(tunnel);
    if (tunnel_cipher_client_need_feedback(ctx->cipher)) {
        socket_read(outgoing); // tunnel_stage_ssr_waiting_feedback;
    }

    do_ssr_receipt_for_feedback(struct tunnel_ctx *tunnel)
        if (feedback) {
            socket_write(outgoing, feedback); // tunnel_stage_ssr_receipt_of_feedback_sent
        }    

do_socks5_reply_success(tunnel);
    socket_write(incoming, ctx->init_pkg); // tunnel_stage_auth_complition_done;

do_launch_streaming(tunnel);
    socket_read(incoming);  // tunnel_stage_streaming
    socket_read(outgoing);  // tunnel_stage_streaming

tunnel_traditional_streaming(tunnel, socket);

tunnel_shutdown(tunnel);
```
