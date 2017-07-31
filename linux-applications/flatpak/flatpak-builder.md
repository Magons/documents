# About
This page notes how to design and run a JSON file that tells flatpak how to build your application, much like a Makefile

# Design

See `man flatpak-builder` for general design tips and `man flatpak-manifest` for field definitions, like the ones seen below.


# Examples

## Retroarch
See [flathub/org.libretro.RetroArch](https://github.com/flathub/org.libretro.RetroArch)
```
{
  "app-id": "org.libretro.RetroArch",
  "runtime": "org.freedesktop.Platform",
  "runtime-version": "1.6",
  "sdk": "org.freedesktop.Sdk",
  "command": "retroarch",
  "rename-desktop-file": "retroarch.desktop",
  "rename-icon": "retroarch",
  "finish-args": [
    "--socket=x11",
    "--socket=wayland",
    "--socket=pulseaudio",
    "--share=ipc",
    "--share=network",
    "--device=all",
    "--filesystem=home",
    "--allow=multiarch"
  ],
  "modules": [
    {
      "name": "retroarch",
      "make-args": [
        "GLOBAL_CONFIG_DIR=/app/etc"
      ],
      "sources": [
        {
          "type": "git",
          "url": "https://github.com/libretro/RetroArch.git",
          "branch": "1.6.3",
          "commit": "adae0656eb4185706c31e5176d73d9215d0856fe"
        },
        {
          "type": "file",
          "path": "org.libretro.RetroArch.appdata.xml"
        },
        {
          "type": "file",
          "path": "retroarch.cfg"
        }
      ],
      "post-install": [
        "mkdir -p /app/share/icons/hicolor/scalable/apps/",
        "mv /app/share/pixmaps/retroarch.svg /app/share/icons/hicolor/scalable/apps/",
        "rmdir /app/share/pixmaps/",
        "mkdir -p /app/etc",
        "cp retroarch.cfg /app/etc",
        "mkdir -p /app/share/appdata",
        "cp org.libretro.RetroArch.appdata.xml /app/share/appdata"
      ],
      "modules": [
        {
          "name": "libpng12",
          "config-opts": ["--disable-static"],
          "cleanup": ["/bin", "/include", "/share", "/lib/libpng.*", "/lib/*.la", "/lib/pkgconfig"],
          "sources": [
            {
              "type": "archive",
              "url": "https://ftp-osl.osuosl.org/pub/libpng/src/libpng12/libpng-1.2.57.tar.xz",
              "sha256": "0f4620e11fa283fedafb474427c8e96bf149511a1804bdc47350963ae5cf54d8"
            }
          ]
        },
        {
          "name": "nvidia-cg-tookit",
          "buildsystem": "simple",
          "build-commands": [
            "mkdir -p /app/lib",
            "install -m 0644 usr/lib64/libCg.so /app/lib",
            "install -m 0644 usr/lib64/libCgGL.so /app/lib"
          ],
          "sources": [
            {
              "type": "archive",
              "url": "http://developer.download.nvidia.com/cg/Cg_3.1/Cg-3.1_April2012_x86_64.tgz",
              "sha256": "e8ff01e6cc38d1b3fd56a083f5860737dbd2f319a39037528fb1a74a89ae9878"
            }
          ]
        }
      ]
    },
    {
      "name": "retroarch-assets",
      "make-install-args": [
        "PREFIX=/app"
      ],
      "sources": [
        {
          "type": "git",
          "url": "https://github.com/libretro/retroarch-assets.git",
          "commit": "8fd7361b09edfe317b8208c1c3051bc2aafa99f9"
        }
      ]
    },
    {
      "name": "libretro-database",
      "make-install-args": [
        "PREFIX=/app"
      ],
      "sources": [
        {
          "type": "git",
          "url": "https://github.com/libretro/libretro-database.git",
          "commit": "8030c7e449d8b7d4e001904138257b22b7b7a18d"
        }
      ]
    },
    {
      "name": "libretro-core-info",
      "make-install-args": [
        "PREFIX=/app"
      ],
      "sources": [
        {
          "type": "git",
          "url": "https://github.com/libretro/libretro-core-info.git",
          "commit": "de4608ca6206698bbb152e49dad16cbfe490c0d3"
        }
      ]
    },
    {
      "name": "retroarch-joypad-autoconfig",
      "make-install-args": [
        "PREFIX=/app"
      ],
      "sources": [
        {
          "type": "git",
          "url": "https://github.com/libretro/retroarch-joypad-autoconfig.git",
          "commit": "47ef1799577c5c57eb8890dc05d3981e7c0b215d"
        }
      ]
    },
    {
      "name": "common-shaders",
      "make-install-args": [
        "PREFIX=/app"
      ],
      "sources": [
        {
          "type": "git",
          "url": "https://github.com/libretro/common-shaders.git",
          "commit": "12eedf894d6116db26bb4cf042db4e8fb24cd640"
        }
      ]
    },
    {
      "name": "slang-shaders",
      "make-install-args": [
        "PREFIX=/app"
      ],
      "sources": [
        {
          "type": "git",
          "url": "https://github.com/libretro/slang-shaders.git",
          "commit": "fd5c3cc64df074ff640c6d2f36d2f0badf73324c"
        }
      ]
    },
    {
      "name": "glsl-shaders",
      "make-install-args": [
        "PREFIX=/app"
      ],
      "sources": [
        {
          "type": "git",
          "url": "https://github.com/libretro/glsl-shaders.git",
          "commit": "860a948ab27efe75de1e2dd1b0ae4416a8a44ddf"
        }
      ]
    },
    {
      "name": "retroarch-filers-video",
      "subdir": "gfx/video_filters",
      "make-install-args": [
        "PREFIX=/app"
      ],
      "sources": [
        {
          "type": "git",
          "url": "https://github.com/libretro/RetroArch.git",
          "commit": "adae0656eb4185706c31e5176d73d9215d0856fe"
        }
      ]
    },
    {
      "name": "retroarch-filers-audio",
      "subdir": "libretro-common/audio/dsp_filters",
      "make-install-args": [
        "PREFIX=/app"
      ],
      "sources": [
        {
          "type": "git",
          "url": "https://github.com/libretro/RetroArch.git",
          "commit": "adae0656eb4185706c31e5176d73d9215d0856fe"
        }
      ]
    }
  ]
}
```
