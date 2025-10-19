# MS-R1 — Run Jellyfin in Docker

## Overview

MS-R1 CPU based on CIX CP8180, a ARM V9 architecture CPU with Arm Immortalis-G720 MC10 GPU.  
CP8180 Supports AV1,H265,H264 video decode and H265,H265 video encode.    

Jellyfin is an open-source media server software that lets you organize, manage, and stream your personal collection of movies, TV shows, music, and photos to multiple devices. It’s similar to Plex or Emby, but completely free and self-hosted.  

You can run Jellyfin with hardware encoding and encoding by this PlayBook  

## Prerequisites
- Docker and Docker Compose set up on the target machine (see: ../PlayBook/MS-R1-How-To-Run-Android-In-Docker.md — use its "Prepare Docker environment" steps).
- Files to copy to the target: `jellyfin-cix.tar.gz` and `docker-compose.yml`.
- A media directory on the host (example: `C:\DATA\Media`).

## Files provided

Download it from:  

- jellyfin-cix.tar.gz — prebuilt Jellyfin image with CP8180 codec libs
- docker-compose.yml — compose file configured for the device and volume mounts

## Steps

1. Copy files to the target MS‑R1 host (e.g. to C:\DATA\jellyfin).

2. Load the Docker image:
    ```bash
    gunzip -c jellyfin-cix.tar.gz | docker load
    # verify
    docker images | grep jellyfin
    ```

3. Start the containers:
    ```bash
    # from the directory with docker-compose.yml
    docker compose up -d
    docker compose ps
    ```

4. Access Jellyfin from a client:
    - Open http://<host-ip>:8096 (e.g. http://172.16.64.53:8096)

5. Add media to the host path (example):
    - Put media under C:\DATA\Media (mounted inside container as /Media)
    - Recommended: ensure permissions match the container user (commonly UID 1000):
    ```powershell
    # Windows: ensure files are accessible to Docker Desktop / WSL backend
    # On Linux hosts:
    sudo chown -R 1000:1000 /DATA/Media
    ```

6. In the Jellyfin web UI, add /Media as a library path and scan your media.

## Troubleshooting

- If docker compose fails with a device error:
  Error response from daemon: error gathering device information while adding custom device "/dev/video2": no such file or directory
  - Edit `docker-compose.yml` and remove the non‑existent device entry under `devices:` for your host.
  - Re-run `docker compose up -d`.

- Check container logs:
    ```bash
    docker compose logs -f jellyfin
    ```

- Verify container is running and ports are bound:
    ```bash
    docker ps --filter "name=jellyfin"
    ```

## Notes
- Hardware decode/encode support depends on CP8180 drivers present in the image and device mappings configured in docker‑compose.
- If you need additional host directories mounted, update `volumes:` in `docker-compose.yml` and restart the stack.
- Ensure you are using Docker Compose V2 (CLI `docker compose`) or adapt commands for `docker-compose` if using V1.

## Example quick tips
- Recreate after compose changes:
    ```bash
    docker compose down
    docker compose up -d --build
    ```

- If you want the compose file reviewed or a suggested example for device/volume mapping, provide your current `docker-compose.yml`.