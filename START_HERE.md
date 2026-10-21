# Start from a new GitHub repository — PocketTracker BR v1

This kit builds a separate **PocketTracker BR** port for **RG28XX / Knulli / AArch64**. It includes the original Mutable Instruments Braids and Rings DSP. All 47 Braids and six Rings model choices have names in the instrument editor.

**This is a source/build kit, not a prebuilt handheld executable.** The full Linux host app and audio tests have passed. GitHub must still build the ARM64 package, and you must test that package on your RG28XX.

## 1. Extract the starter download once

Extract `PocketTracker_BR_v1_Start_Here.zip` on your computer. Inside you will find:

| File | What to do with it |
|---|---|
| `PocketTracker_BR_v1_Source.zip` | Upload this ONE file to GitHub. Keep it zipped. |
| `build-braids-rings.yml` | Open as plain text and paste into a new GitHub workflow file. |
| `START_HERE.md` | These instructions. |
| `BR_ENGINE_GUIDE.md` | Controls, model list, test status and technical limitations. |

Do not drag the extracted source folder into GitHub. You do not need to upload thousands of files. GitHub stores the source ZIP as a file; the workflow extracts it inside the build runner. The workflow itself must exist outside the ZIP in `.github/workflows/`.

## 2. Make a completely new repository

1. Sign into GitHub and open https://github.com/new.
2. Name it `pockettracker-braids-rings` (or another unused name).
3. Choose Public or Private as you prefer. Private repositories are subject to your account's Actions usage allowance.
4. Enable **Add a README file**, then click **Create repository**. This gives you a default branch, normally `main`.
5. Stay on the repository's **Code** tab.

## 3. Upload the source ZIP

1. Click **Add file → Upload files**.
2. Choose **only** `PocketTracker_BR_v1_Source.zip` from the extracted starter folder. Do not select the outer starter ZIP.
3. Wait for the upload to finish.
4. Commit directly to `main` (or your default branch).
5. Confirm the repository root shows `README.md` and `PocketTracker_BR_v1_Source.zip`. Do not rename the ZIP or put it in another folder.

The supplied source ZIP is below GitHub's 25 MiB browser upload limit. Uploading this single ZIP also avoids the 100-file limit.

## 4. Create the new workflow

Use **this kit's new workflow** for this new repository.

1. On **Code**, choose **Add file → Create new file**.
2. In the filename box, enter exactly:

   `.github/workflows/build-braids-rings.yml`

   Typing the slashes creates the folders. You do not need to find an existing workflows folder.
3. On your computer, open the supplied `build-braids-rings.yml` in a plain-text editor, such as Notepad or VS Code. Select all and copy.
4. Paste the text into GitHub's editor. Its first line must be:

   `name: Build PocketTracker BR v1 (Braids + Rings)`

5. Click **Commit changes** and commit directly to `main`.

Paste the plain YAML exactly. Do not paste a table, Markdown code fences, vertical `|` table borders, or HTML entities such as `&#x20;` / `&#xA;`. Those were the formatting problem in the earlier attempt. The `.yml` extension must not become `.yml.txt`.

## 5. Run the build

1. Open the **Actions** tab. Enable Actions if GitHub asks.
2. In the left sidebar, select **Build PocketTracker BR v1 (Braids + Rings)**.
3. Click **Run workflow**, select `main`, then click the green **Run workflow** button.
4. Refresh if the new run does not appear immediately. Open the run, then the `portmaster` job to view progress.
5. Wait for a green success tick. The first build may take several minutes; the workflow allows up to 90 minutes.

The build verifies the source ZIP's exact checksum, tests all 53 models, cross-compiles in an Ubuntu 20.04 container, checks the AArch64 executable and glibc requirement, then searches the actual binary for original Braids/Rings lookup tables and the model labels. An older binary with only a changed filename is rejected.

## 6. Download the correct result

1. Open the successful run's **Summary** page.
2. Scroll to **Artifacts**.
3. Download the artifact named `PocketTracker-BR-v1-RG28XX-run-...-attempt-...` from this run. You must be signed into GitHub.
4. Extract the downloaded artifact ZIP. It contains:
   - `pockettracker_br.zip` — the handheld installation package;
   - `BUILD_INFO.json` — the source/binary hashes and run identity;
   - `SHA256SUMS.txt` — checksum for the installation ZIP.

The artifact wrapper and the port installation ZIP are two different ZIPs. Do not install the original source ZIP or the starter download on the handheld.

## 7. Install on Knulli

1. Make sure PortMaster is installed. On current Knulli, use **START → Device Settings → Install Portmaster** if needed; some older releases provide an **Install.PortMaster** entry in Ports. Launch PortMaster once to finish any setup.
2. Shut down safely before removing the SD card. Access the active Knulli ROM storage on your computer using the card or your usual network share.
3. Open the `roms/ports` folder. This is the Ports folder on the active ROM storage, not the boot partition.
4. Extract **`pockettracker_br.zip`** so its contents sit directly in that folder. You must end up with these two items side by side:
   - `roms/ports/PocketTracker BR.sh`
   - `roms/ports/pockettracker_br/` (containing `pockettracker.aarch64`)
5. Do not add an extra outer folder around those two items. Keep the binary inside `pockettracker_br`; do not rename it.
6. Safely eject/reinsert the card if used, start Knulli and refresh/update the game list or restart EmulationStation.
7. Open **Ports → PocketTracker BR**. The launcher is distinct from the old PocketTracker entry. It creates its own data folder on first run.

Alternative: if you already use PortMaster's `autoinstall` folder successfully, place the intact inner `pockettracker_br.zip` there and launch PortMaster. Do not do both installation methods.

## 8. Check the synths in the app

1. Start with a new song and one synth track. Use the normal SONG → CHAIN → PHRASE flow to enter a note/instrument.
2. Open **INSTRUMENT** (R + D-pad navigates screens). Select the TYPE value, then hold A and use up/down to choose **BRAIDS**.
3. On MODEL, you should see a number **and a name**, initially `0 CSAW`. Change it using A + up/down. There are 47 models, numbered 0–46.
4. Play a middle-register note and change TIMBRE/COLOR. No sample file is needed.
5. Switch TYPE to **RINGS**. On MODEL, select `0 MODAL RESONATOR`; there are six choices, 0–5. Change STRUCTURE, BRIGHTNESS, DAMPING and POSITION, then retrigger the note.
6. Save a project, reopen it and verify the selected engine/model. Add more tracks gradually while checking for audio crackles.
7. Quit using **PROJECT → EXIT**.

See `BR_ENGINE_GUIDE.md` for every model name and the limits of this first integration. The engines use the original DSP, but the adapter does not reproduce every hardware firmware feature or every PocketTracker modulation option.

## If something goes wrong

| Problem | What to check |
|---|---|
| “Yowza, that's a lot of files” | You selected extracted source files. Upload only `PocketTracker_BR_v1_Source.zip`. |
| No workflows folder | Create it by typing the full filename in step 4. |
| No workflow listed in Actions | Check the file is exactly `.github/workflows/build-braids-rings.yml`, outside the source ZIP, committed on the default branch. |
| No Run workflow button | The YAML must include `workflow_dispatch` and be present on the default branch. Refresh the page and check Actions is enabled. |
| YAML syntax error | Replace the whole workflow with the supplied plain `.yml` contents. Remove any table borders, HTML entities or code fences. |
| Source checksum failure | Upload the exact supplied source ZIP with its exact name. A renamed old kit is not the new source. |
| Red build | Open the failed step and copy its error text or download the run logs. Do not install an artifact from a different run to work around it. |
| PocketTracker BR missing in Ports | Check the `.sh` and `pockettracker_br` folder are directly inside `roms/ports`; refresh the game list. |
| App returns immediately to Ports | Send `roms/ports/pockettracker_br/log.txt` and, if present, `pockettracker_br/data/pockettracker-log.txt`, plus `BUILD_INFO.json`. |
| Still see MACRO or no Rings | Check that you launched **PocketTracker BR**, downloaded the new run artifact, and installed its inner ZIP. |
| Crackles with many synth tracks | Start with one track and add tracks gradually; report engine, model, tempo and number of active tracks. Device performance is not verified yet. |

The source archive is intentionally pinned by checksum. If we change its code later, use the matching updated workflow too.

## References

- GitHub browser uploads: https://docs.github.com/en/repositories/working-with-files/managing-files/adding-a-file-to-a-repository
- GitHub manual workflow runs: https://docs.github.com/en/actions/how-tos/manage-workflow-runs/manually-run-a-workflow
- Knulli PortMaster: https://knulli.org/systems/portmaster/
- Knulli ROM storage: https://knulli.org/play/add-games/game-storage/
- PortMaster: https://portmaster.games/installation.html
