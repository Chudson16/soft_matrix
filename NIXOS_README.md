# How to use this on Nix (Or at least how I am using it)
### I'm not using flakes so this may be a bit convoluted

Create the folder `/etc/nixos/pkgs/soft-matrix`

Create the file `/etc/nixos/pkgs/soft-matrix/default.nix`

Paste the following in the `default.nix` file

```nix
{ lib
, rustPlatform
, fetchFromGitHub
, git
}:

rustPlatform.buildRustPackage rec {
  pname = "soft-matrix";
  version = "1.0.5"; # or "unstable"
  src = fetchFromGitHub {
    owner = "Chudson16";
    repo = "soft_matrix";
    rev = "63c6cae";
    hash = "sha256-ERyJNUb8YHOEX2oyeyoyRH9DkMFJ5rjIUJPTioCGDRE";
  };
  nativeBuildInputs = [ git ];
  cargoLock.lockFile = "${src}/Cargo.lock";
  cargoHash = "sha256-ERyJNUb8YHOEX2oyeyoyRH9DkMFJ5rjIUJPTioCGDRE";
  meta = with lib; {
    description = "Stereo to 5.1 upmixer";
    homepage = "https://github.com/Chudson16/soft_matrix";
    license = licenses.mit; # adjust if your fork differs
    platforms = platforms.linux;
  };
}
```

In `/etc/nixos/configuration.nix`, add the following

```nix
{
...
nixpkgs.overlays = [
  (final: prev: {
    soft-matrix = prev.callPackage ./pkgs/soft-matrix { };
  })
];
...
}
```

Still in your `configuration.nix`, add soft-matrix to your list of packages

```nix
environment.systemPackages = with pkgs; [
  soft-matrix
];
```
