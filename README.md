# debloated-mesa

Combined Mesa + Vulkan drivers + minimal `libLLVM.so` package — replaces the full `mesa`, `vulkan-*` and `llvm-libs` stack with a single ~35 MiB `.pkg.tar.zst`.

## Usage

```sh
sudo pacman -U mesa-llvm-mini-x86_64.pkg.tar.zst
```

## Build locally

```sh
makepkg -si
```

## CI

Weekly builds for `x86_64` and `aarch64` published to the `continuous` tag.
