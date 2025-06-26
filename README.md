# texMini: Ultra-lean LaTeX with Smart Package Loading

A minimal TeX Live distribution (~41MB) with intelligent on-demand package loading.

## Quick Start

```bash
# Basic compilation (minimal LaTeX)
nix shell .#texMini -c latexmk -pdf document.tex

# Add packages on-the-fly with --extra
nix shell .#texMini -c latexmk -pdf document.tex --extra biber microtype fontspec

# Continuous compilation with preview
nix shell .#texMini -c latexmk -pdf -pvc document.tex --extra tikz-cd biblatex

# Works with any latexmk options
nix shell .#texMini -c latexmk -lualatex document.tex --extra fontspec unicode-math
```

## Features

- **🚀 Ultra-lean**: <100 MB base installation with essential packages
- **🧠 Smart loading**: Add packages dynamically with `--extra package1 package2`
- **⚡ Fast**: Pre-built variants for common use cases
- **🔄 Compatible**: Works with all latexmk options and compilation modes
- **📦 Reproducible**: Pinned nixpkgs for consistent builds

## Pre-configured Variants

Skip the `--extra` flag for common document types:

```bash
# Bibliography documents (includes biblatex, biber, csquotes)
nix shell .#texMiniBiblio -c latexmk -pdf paper.tex

# Typography-focused (includes microtype, fontspec, unicode-math)
nix shell .#texMiniTypo -c latexmk -pdf article.tex

# Graphics/TikZ documents (includes tikz-cd, pgfplots, circuitikz)
nix shell .#texMiniGraphics -c latexmk -pdf diagrams.tex
```

## Advanced Usage

### Using in Your Own Flake

```nix
{
  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-24.05";
    texMini.url = "github:yourusername/texMini";
  };
  
  outputs = { self, nixpkgs, texMini }:
    nixpkgs.lib.genAttrs [ "x86_64-linux" "aarch64-darwin" "x86_64-darwin" ] (system:
      let
        pkgs = import nixpkgs { 
          inherit system; 
          overlays = [ texMini.overlays.default ];
        };
        
        # Use texMini with custom packages
        myTexLive = pkgs.texMiniWith {
          extra = [ "beamer" "tikz-cd" "biblatex" "biber" ];
        };
      in {
        devShells.default = pkgs.mkShell {
          buildInputs = [ myTexLive ];
        };
      });
}
```

## What's Included

### Base Minimal Set
- **Core**: `scheme-infraonly`, `latex-bin`, `latexmk`
- **Math**: `amsmath`, `amsfonts`, `amscls`  
- **Essential**: `geometry`, `hyperref`, `xcolor`, `graphics`
- **Language**: `babel` (basic multilingual support)
- **Graphics**: `pgf` (TikZ), `framed`
- **Dependencies**: Common packages needed by the above

### Pre-configured Additions
- **texMiniBiblio**: `biblatex`, `biber`, `csquotes`
- **texMiniTypo**: `microtype`, `fontspec`, `unicode-math`  
- **texMiniGraphics**: `tikz-cd`, `pgfplots`, `circuitikz`

## Common Package Names

When using `--extra`, here are some frequently needed packages:

- **Bibliography**: `biblatex`, `biber`, `csquotes`, `natbib`
- **Advanced graphics**: `tikz-cd`, `pgfplots`, `circuitikz`, `forest`
- **Typography**: `microtype`, `fontspec`, `unicode-math`, `polyglossia`
- **Document classes**: `beamer`, `memoir`, `koma-script`, `moderncv`
- **Math**: `mathtools`, `amsthm`, `thmtools`, `physics`
- **Tables**: `booktabs`, `longtable`, `array`, `tabularx`
- **Code**: `listings`, `minted`, `fvextra`

## Package Discovery

To find TeX Live package names:
- Search [CTAN](https://www.ctan.org/pkg) 
- Use `tlmgr search --global <package>` in any TeX Live installation
- Check the [TeX Live package database](https://tug.org/texlive/pkgcontrib.html)

## Size Comparison

How does texMini compare to other LaTeX distributions?

| Distribution | Base Size | Notes |
|--------------|-----------|-------|
| **texMini** | **~41MB** | This project - measured with `nix eval --file weigh.nix` |
| TinyTeX | ~200MB | R-focused minimal TeX Live |
| MiKTeX Basic | ~200MB | Windows-focused basic installation |
| BasicTeX (MacTeX) | ~100MB | macOS minimal TeX Live subset |
| TeX Live Scheme-Basic | ~300MB | Official TeX Live basic scheme |
| TeX Live Full | ~5-7GB | Complete TeX Live installation |

### Verification

You can verify texMini's size claims yourself:

```bash
# Check sizes of all packages in this flake
nix eval --file weigh.nix

# Build and measure a specific package
nix build .#texMini && du -sh result
```

## Why texMini?

- **Size**: Standard TeX Live is 5+ GB, texMini is just ~41MB
- **Speed**: Faster downloads and builds
- **Flexibility**: Add exactly what you need, when you need it  
- **Reproducible**: Pinned dependencies, consistent across machines
- **Smart**: The `--extra` flag makes package management effortless