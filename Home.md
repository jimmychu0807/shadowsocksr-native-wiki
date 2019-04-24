Welcome to the shadowsocksr-native wiki!

Client logic.
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
```
