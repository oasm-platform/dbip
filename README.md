# 🌍 GeoIP Proxy

> 📅 DB-IP Lite databases last updated: **2026-11-01**

GeoIP Proxy is a simple service that provides IP geolocation data using the free [DB-IP Lite](https://db-ip.com/db/lite.php) databases (City + ASN, MMDB format). No account or license key is required.

## 🚀 Run with Docker

The Docker image is rebuilt daily and always ships the latest monthly DB-IP Lite release. It is published to [Docker Hub](https://hub.docker.com/r/oasm/dbip) and GitHub Container Registry:

| Registry | Image |
| --- | --- |
| Docker Hub | `oasm/dbip` |
| GHCR | `ghcr.io/oasm-platform/dbip` |

To run the service using Docker, simply run:

```sh
docker run -d --name dbip -p 4360:4360 oasm/dbip:latest
```

To use a different port, set `PORT` and map the same port:

```sh
docker run -d --name dbip -e PORT=8080 -p 8080:8080 oasm/dbip:latest
```

The image is built for `linux/amd64`. On ARM machines (e.g. Apple Silicon), add `--platform linux/amd64`.

### Tags

| Tag | Description |
| --- | --- |
| `latest` | Most recent build |
| `<yymmdd>` | Build of a given day, e.g. `261103` |
| `<sha>` | Build of a given commit (first 7 characters of the commit SHA) |

### Docker Compose

```yaml
services:
  dbip:
    image: oasm/dbip:latest
    container_name: dbip
    ports:
      - "4360:4360"
    restart: always
```

## 🌐 Access the API

Once the service is running, you can access it by opening a web browser or using `curl`:

```
http://localhost:4360/141.101.64.0
```

Example using `curl`:

```sh
curl http://localhost:4360/141.101.64.0
```

| Endpoint | Description |
| --- | --- |
| `GET /:ip` | Geolocation for a single IPv4/IPv6 address |
| `GET /bulk/:ips` | Geolocation for comma-separated IPs, e.g. `/bulk/1.1.1.1,8.8.8.8` |
| `GET /` | Returns the caller's IP address |

## 📜 Example Response

Upon a successful request, the API returns a JSON response containing IP geolocation details:

```json
{
  "query": "141.101.64.0",
  "status": "success",
  "continent": "Europe",
  "continentCode": "EU",
  "country": "The Netherlands",
  "countryCode": "NL",
  "region": "",
  "regionName": "North Holland",
  "city": "Amsterdam",
  "district": "",
  "zip": "",
  "lat": 52.3676,
  "lon": 4.90414,
  "timezone": "Europe/Amsterdam",
  "offset": 7200,
  "currency": "",
  "isp": "Cloudflare Inc",
  "org": "Cloudflare Inc",
  "as": "AS202623 Cloudflare Inc",
  "asname": "Cloudflare Inc"
}
```

If the IP is not in the database, the API returns `{ "error": "IP not found" }`.

## 🔄 Migrating from GeoLite2

Earlier versions used MaxMind GeoLite2. The API endpoints and response shape (field names and types) are unchanged, but the free DB-IP Lite data has fewer details:

| Field | Change |
| --- | --- |
| `region`, `zip`, `district` | Always `""` (not included in DB-IP Lite) |
| `timezone` | Derived from the coordinates, as DB-IP Lite has no time zone |
| `offset` | Now the current UTC offset in seconds (previously the GeoLite2 accuracy radius) |
| `as` | `""` when the ASN is unknown (previously `"ASundefined undefined"`) |

You can still use your own GeoLite2 files: mount a directory containing `GeoLite2-City.mmdb` and `GeoLite2-ASN.mmdb` to `/usr/share/GeoIP` (or point `GEOIP_DB_PATH` at it). DB-IP files (`dbip-city-lite.mmdb`, `dbip-asn-lite.mmdb`) take precedence when both are present.

## ⚙️ Configuration

| Variable | Default | Description |
| --- | --- | --- |
| `PORT` | `4360` | Server port |
| `GEOIP_DB_PATH` | `/usr/share/GeoIP/` | Directory containing the `.mmdb` files |

## 📜 License

[IP Geolocation by DB-IP](https://db-ip.com), licensed under [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/).

## 📞 Support

For any issues or questions, please create an issue in the [GitHub repository](https://github.com/oasm-platform/dbip/issues).
