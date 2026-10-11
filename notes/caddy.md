# Intro

Web server and reverse proxy written in Go

By default, Caddy automatically obtains and renews TLS certificates for all your sites.

Webserver- Listens for HTTP/HTTPS requests. Processes those requests. Returns HTTP responses. Implements the HTTP protocol.

Caddyfile- Caddy’s configuration file

Alternatives: Nginx, Apache HTTP Server


# Example

```
#############################################
# GLOBAL OPTIONS BLOCK
#############################################

{
    # Email used for Let's Encrypt registration
    email admin@example.com

    # Admin API config
    admin localhost:2019
}

#############################################
# MAIN WEBSITE (STATIC + REVERSE PROXY)
#############################################

example.com, www.example.com {

    #########################################
    # Logging
    #########################################
    log {
        output file /var/log/caddy/access.log
        format json
        level INFO
    }

    #########################################
    # Enable compression
    #########################################
    encode gzip zstd

    #########################################
    # Redirect www → non-www
    #########################################
    @www host www.example.com
    redir @www https://example.com{uri}

    #########################################
    # Static files
    #########################################
    root * /var/www/html
    file_server

    #########################################
    # Custom headers (security hardening)
    #########################################
    header {
        Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
        X-Content-Type-Options "nosniff"
        X-Frame-Options "DENY"
        Referrer-Policy "strict-origin-when-cross-origin"
    }

    #########################################
    # Custom error handling
    #########################################
    handle_errors {
        @404 {
            expression {http.error.status_code} == 404
        }
        rewrite @404 /404.html
        file_server
    }
}

#############################################
# API BACKEND (REVERSE PROXY)
#############################################

api.example.com {

    encode gzip

    #########################################
    # Rate limiting (requires module)
    #########################################
    # rate_limit {
    #     zone api 10r/s
    # }

    #########################################
    # JWT / Header validation example
    #########################################
    @internal {
        header X-Internal-Request true
    }

    #########################################
    # Route block (manual directive ordering)
    #########################################
    route {

        # Handle internal requests differently
        handle @internal {
            reverse_proxy localhost:9001
        }

        # Default API backend
        reverse_proxy localhost:8080 {
            lb_policy round_robin

            health_uri /health
            health_interval 10s
            health_timeout 2s
        }
    }

    #########################################
    # TLS customization
    #########################################
    tls {
        protocols tls1.2 tls1.3
    }
}

#############################################
# ADMIN DASHBOARD WITH BASIC AUTH
#############################################

admin.example.com {

    # Basic Auth (bcrypt hash)
    basicauth {
        admin $2a$14$examplebcryptpasswordhash
    }

    reverse_proxy localhost:4000
}

#############################################
# INTERNAL SERVICE (HTTP ONLY)
#############################################

:8081 {

    # Explicitly disable automatic HTTPS
    tls internal

    reverse_proxy localhost:5000
}

#############################################
# REDIRECT HTTP TO HTTPS (Explicit)
#############################################

http://old-domain.com {
    redir https://example.com{uri} permanent
}
```

```
# Main domain (Frontend)
{env.MAIN_DOMAIN} {
    reverse_proxy localhost:{env.FRONTEND_PORT}
}

# API service
{env.API_SUBDOMAIN}.{env.MAIN_DOMAIN} {
    reverse_proxy localhost:{env.API_PORT}
}

# Admin service
{env.ADMIN_SUBDOMAIN}.{env.MAIN_DOMAIN} {
    reverse_proxy localhost:{env.ADMIN_PORT}
}

# Auth service
{env.AUTH_SUBDOMAIN}.{env.MAIN_DOMAIN} {
    reverse_proxy localhost:{env.AUTH_PORT}
}
```
