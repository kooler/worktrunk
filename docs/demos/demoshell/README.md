# Try it demo

The [Try it](https://worktrunk.dev/try-it/) page embeds a [Demoshell](https://demoshell.com) VM: Alpine Linux on an emulated arm64 CPU, running in the visitor's browser with `wt` installed. `docs/src/pages/try-it.astro` mounts it by its Demoshell reference.

The VM's definition is kept here and entered into the Demoshell studio, where the VM is built and saved:

| File | Studio field |
|------|--------------|
| `recipe.sh` | Base image script |
| `after-load.sh` | VM startup → A shell prompt → After VM load |
| `guide.json` | Guide: title, order, finish card, and steps |

`guide.json` uses the field names Demoshell's embed API serves for a guide. Each step ticks when the visitor runs `pattern`, and `output_contains` must appear in its output, so check those strings against real output when changing the recipe or `wt`'s messages.

To check the full recipe before a Studio build, run this from the repository root with Docker:

```bash
docker run --rm -it --platform linux/arm64 \
  -v "$PWD/docs/demos/demoshell:/demo:ro" \
  alpine:latest sh -ec 'sh -e /demo/recipe.sh; exec bash -l'
```

In the container, run `source /demo/after-load.sh`, then each step's `pattern` from `guide.json`. Check that each step's `output_contains` appears and that merging returns the shell to `/root/acme` with the updated price. This exercises the release download and Alpine shell setup as well as the guide commands; the Studio build and automatic guide completion still need checking in Demoshell.

Demoshell rebuilds the VM when a Worktrunk release is published, so the recipe installs the latest release rather than a pinned version.
