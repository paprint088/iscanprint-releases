# iScan&Print (C# / WinForms, .NET 8)

Owner and developer: **XIS Systems** · ©2026 2027 XIS Systems · Version 1.0.0 (set by `<Version>` in `IdCopyStation.csproj`).

The window header shows the app icon (130% of its title-bar size), the name, *by XIS Systems* and the **About iScan&Print** link, which opens a small window that stays on top of all windows (name, version, copyright, description and a **Close** button).

**Copy mode** scans ID cards and booklets on a WIA flatbed, detects and straightens the original,
crops it and arranges it on an A4 sheet at true size.
**Print mode** turns uploaded photos/images/PDFs of documents into clean, flat, upright A4 pages.
Output is printed (**Copy** / **Print**) or saved (**Save to File**, PDF or JPG).

## Build & run
1. Install the **.NET 8 SDK** (Windows 10/11, x64).
2. In this folder run:
   ```
   dotnet restore
   dotnet run -c Release
   ```
   A Release build needs the licence settings filled in (see *Account & licence*); for development
   without a licence server use `dotnet run` (Debug), which runs unlocked.
   **Standalone EXE** (runs on a PC without .NET): double-click `publish.cmd`, or run
   `dotnet restore -r win-x64` then `dotnet publish -c Release -p:PublishProfile=Standalone`.
   The result is `publish\iScanPrint.exe` (copy the whole `publish` folder). Every publish names the program **iScanPrint.exe**; project folders, the source and the `%AppData%\IdCopyStation` data folders keep their names, so saved prices and transactions carry over. (`dotnet run` during development still runs as IdCopyStation.) The first publish needs internet
   to download the .NET runtime packs; **NU1100** means NuGet could not download them – check the connection,
   run `dotnet nuget locals all --clear`, and try again (the project's `NuGet.Config` points NuGet at nuget.org).
3. NuGet packages: OpenCvSharp4 (+ runtime.win, Extensions) for image processing, PDFsharp for PDF output.
   Scanning uses Windows WIA; uploaded PDFs use the Windows PDF engine; text orientation uses Windows OCR.

## Main window
The sections **Mode**, **Options**, **Scan Setting** and **Print Setting** have no boxes around them – just a bold heading; only the Files drag & drop area keeps its box.
The window can be resized (not smaller than its opening size) and maximized. All boxes stretch to the window width – except the three compact Options boxes, which stay at their minimum size – and extra height goes to the file list in the Files box.

| Group | Controls |
|---|---|
| **Mode** | **Copy** (scan originals) or **Print** (process uploaded files); **Cart** tick (off by default, right end of the row) with the **Pricing matrix** link below it; and the **Files** box: **File Upload…** / Clear, or **drag & drop** images, PDFs or a folder into the box. In Print mode dropped files are added to the list in drop order; for Extract from Copy the dropped file replaces the current one. |
| **Options** | Three columns side by side, without boxes – headed *ID Cards*, *Booklets* and one without a heading, two option rows high, one choice: **ID Cards** – One Side, Back to Back (scan size locked to **A6**); **Booklets** – One Side, Back to Back, and Multiple Pages on the 2nd row (scan size locked to **A5**); and an unnamed box with **Documents** and **Extract from Copy** (full-page size dropdown). Extract from Copy is Copy mode only; all others work in both modes. |
| **Scan Setting** | A6 / A5 / full page with a size dropdown: **Letter** (8.5"×11"), **A4** (210×297 mm), and – only when ADF is ticked – **Long** (8.5"×13") and **Legal** (8.5"×14"), note *“Place original to the &lt;size&gt; position of flatbed”*, scan Resolution 300 (default) / 600 dpi, **ADF (document feeder)** – shown only if the selected scanner has a feeder, used by Documents – and **Select Scanner…**. All greyed out in Print mode (Print mode works at a fixed 300 dpi). |
| **Print Setting** | Enlarge to 120% (off), **B&W** (off – every output sheet, printed or saved, in black & white), Preview before output, **Paper Size** (A4 default, Letter, Long 8.5"×13", Legal 8.5"×14" – used by Documents), Printer, Copies, **Collate** (ticked automatically when copies > 1, disabled at 1 copy). After a successful **Copy** or **Print**, Copies goes back to **1** for the next job (amounts already in the cart keep their number of copies). |
| Buttons | **Save to File**, **Copy** (Copy mode), **Print** (Print mode). |

## Copy mode options
| Option | Behaviour |
|---|---|
| One Side (ID Cards / Booklets) | One scan, centred on A4. Large booklets (passport, seaman's book…) are **not rotated**; if too big they are shrunk to fit. |
| Back to Back | Two scans. **IDs**: front above back, centred, 40 mm gap. **Booklets**: cover in the upper half, inside page in the lower half; small booklets (e.g. yellow card) turned 90° if that fits better. |
| Extract from Copy | Scans the whole page (Letter or A4), or uses the first uploaded file, crops the booklet image off the copy and centres it on A4. **Save to File is disabled**. |
| Multiple Pages (Booklets) | Page by page, size locked to **A5**, Enlarge greyed out. Each page turned upright (portrait) and centred on A4. **Copy** prints after every scan; **Save to File** saves all pages at the end. |
| Documents | Full-page copies of loose pages, each fitted to the selected **Paper Size**. **Flatbed**: full-page size (Letter or A4), one page per scan, Yes = next page / No = finish. **ADF** (if ticked): every page in the feeder is scanned at the Scan Setting size – Letter, A4, Long or Legal. **Copy** prints all pages as one job with copies/collate; **Save to File** saves them as PDF or JPG. |

## Print mode
Upload one or more photos, images, PDFs or **Word documents (.doc, .docx)** – every PDF / Word page counts as one image. Word files are converted to PDF once (with Microsoft Word if installed, otherwise LibreOffice) and then previewed, printed, saved and priced exactly like a PDF; the converted copy is cached in `%LocalAppData%\IdCopyStation\converted` until the document changes.

| Option | Behaviour |
|---|---|
| **Documents** | Each image is one document page, fitted (enlarged or reduced) to the selected **Paper Size**. |
| **ID Cards – One Side** | Each image is one ID card, printed at ID card size (85.6 mm long side) in the centre of A4. Enlarge to 120% is available (off by default). |
| **Multiple Pages** | Each image is one booklet page (passport, seaman's book, yellow card…): page edges found, perspective corrected, text turned upright, then centred on its own A4 sheet. A photo has no real size, so the page is made as large as the Copy – Booklet A5 scan area (148×210 mm portrait, 210×148 mm for an opened spread). Enlarge is off. |
| **ID Cards – Back to Back** | Images are taken in pairs – front, back, front, back… Front above back, centred on A4 with a 40 mm gap. An unpaired last image is printed alone. Enlarge to 120% is available. |
| **Booklets – One Side / Back to Back** | Booklet photos (booklet page detection). One Side: centred on A4. Back to Back: images in pairs – cover, inside… – cover in the upper half, inside page in the lower half, turned to fit. Sized like an A5 booklet scan. |

Processing for every image:
1. **Edge & corner detection** – finds the page/card outline to separate it from the table or floor (strong edges first, then faint ones for a white page on a light desk). Only realistic outlines are accepted (corners 60–120°, opposite sides of similar length) and the **largest** one is used; for ID cards the outline must also have card proportions, long side 1.42–1.76 × short side (so a QR code, photo or printed box on the card, or the pop-up window around a digital ID in a phone screenshot, is never taken for the card). Outlines seen straight on (screenshots, scans) are cropped to their exact rectangle, so rounded card corners don't skew the crop. Soft outlines are searched again at a smaller size. For **booklets** the "paper" area (light, low colour) is also used, so an opened passport is found as a whole spread even when its two pages differ in colour or run off the photo. On a plain or white background (faint page edges), everything that differs from the background colour around the photo's edge is taken as the booklet. If the photo is **already cropped to the page** (paper all around the edge), the geometry is left untouched.
2. **Perspective correction** – warps it to look scanned flat from directly overhead (A4/Letter or ID-card proportions are snapped when close).
3. **Clean-up filter** – *Magic Color*, *No Shadow*, *B&W Document* or *Original* (choose *Original* or *No Shadow* if a card photo looks too harsh).
4. **Upright text** – Windows OCR tries 0/90/180/270° and keeps the rotation with the most real words (only sideways pages are fixed if no OCR language is installed).
5. **Filter & Crop** – chosen per page in the **Files** page viewer (click the file name, then click a page):
   - **Filter** – Original (default), Magic Color, No Shadow or B&W Document, each with sliders: *Shadows* (0–100 % shadow removal), *Brightness*, *Contrast*, *Saturation*, *Sharpness* (Magic Color / Original), *Threshold* (B&W – more or less ink). **Reset adjustments** returns to the filter's standard look; **Use this filter on all pages** copies it to every page of the file.
   - **Perspective crop…** – drag the four corners onto the page or card; the area is straightened as if scanned from above. **Auto-detect page** / **Use whole image**.
   - **Resize – 100% +** – click **-** or **+** to change the page's size on the sheet in 10% steps (10–300%, 100% = normal placement). The page grows or shrinks around the centre of its place; anything beyond the paper is cut off.
   - **Rotate** – **Left 90°** / **Right 90°** turns the page on the sheet.
   - The page is shown on the right **on the sheet, as it will print** (cropped, filtered, upright, resized and rotated), and its **thumbnail** in the page list changes to the same result. Closing the viewer with **X** after changes asks whether to keep them (OK keeps, Cancel discards), and each adjusted page shows its setting under its thumbnail. All changes are rebuilt from the original page, kept per page, used for pricing, printing and saving, and saved with locked cart items (restored by Recall).
   - The *Filter* Additional Charge is added per page that has a filter other than Original.
6. **Preview** (Preview before output): **Use page** and **Skip page** (rotating is done in the page viewer; Copy-mode scans, which have no page viewer, can still be rotated here).

**Print** sends all sheets as one job with the chosen copies and collate setting. After a successful print the **Files list is cleared** (with its page selections, filters, crops and sizes) ready for the next job; the charged items stay in the cart. If the print is cancelled or fails, the files stay.

## Cart & pricing
- Tick **Cart** to charge jobs: the Cart window opens and every finished print or save is added as an item with its charge in Peso, plus the running **Total**. Closing the window unticks Cart (items are kept; tick again to show them). Jobs done while Cart is unticked are not charged.
- **Check out after a job:** while the Cart window is open, every successful **Save to File**, **Copy** or **Print** ends with the question *Also check out now?* (item count and total shown). **Yes** checks out at once (transaction saved, cart cleared); **No** keeps the cart going for more jobs. Cancelled or failed jobs don't ask.
- **Check Out** saves the transaction (after confirmation) to `%AppData%\IdCopyStation\Transactions\transactions.csv` and clears the cart. **New Cart** clears the cart without saving (after confirmation).
- **Bluetooth** (in the Files box): tick it to receive files from a phone. Windows' own *Receive files* window opens; on the phone choose **Share → Bluetooth → this PC**. When Windows asks where to save, keep the suggested folder (Documents) or the one set with **Receive folder…**. Each received file is added to the Files list automatically (images, PDF, Word), and the window opens again for the next file. Untick, or close the window, to stop. Pair the phone with the PC once in Windows Settings → Bluetooth & devices.
- Every uploaded file name is a **link**: click it to view all pages of the file. Each page has a small check (checked by default); **uncheck pages** to leave them out of pricing, printing and saving. **Click a page** to set its **Filter** and **Crop** (panel on the right), double-click to see it larger. The selection is kept with locked cart items and restored by Recall; a file already costed on upload is costed again with the new selection.
- Every uploaded file is listed in the Files box by its file name (click a name to view its pages).
- **When items are added:** in Print mode the uploaded files are calculated as soon as they are uploaded (with Cart ticked) and shown in the cart as **Estimate** – Documents one item per file, ID Cards / Booklets one item for the whole upload. When the job **runs successfully** (Print, Copy or Save to File) the estimate is replaced by the final item, **locked**. In **Copy mode** – and in **Print mode until files are uploaded** – the cart shows an **Estimate** of the next job as soon as Cart is ticked, from the selected and ticked options (option, Color/B&W, copies, paper size, 600 dpi, Enlarge): one sheet for ID Cards / Booklets One Side or Back to Back, per page for Multiple Pages and Documents, and "from ₱…" (lowest ink tier) for colour Documents / Extract from Copy. It follows every setting change; when the job runs successfully it is replaced by the final item, locked, priced from what was actually scanned or uploaded. In Print mode, uploading files replaces it with the estimates calculated from the files. A cancelled or failed job adds nothing.
- **Save to File overrides the price:** a saved job is charged with the *Save to File* price per page – also when it replaces an estimate the user locked, or a recalled item.
- **Double-click an item** in the Cart to **lock** it (after confirmation): its amount and details are frozen (copies, pricing or setting changes no longer affect it) and the job settings are saved with it (mode, option, scan size, resolution, ADF, filter, enlarge, B&W, preview, paper size, printer, copies, collate and the files). Locked items are highlighted.
- **Double-click a locked item** to **Recall** its settings into the main window – then press **Save to File**, **Copy** or **Print**; that run is not charged again because the item is already in the cart. Or choose **Unlock**.
- **Pricing matrix** opens an editable view of all price matrices (one tab each). **Save** writes them to the pricing files in `%AppData%\IdCopyStation\Pricing\` (one JSON file per matrix, created with starting prices on first run).

| Matrix | Used for | Priced by |
|---|---|---|
| Save to File | Save to File, every mode/option | price per saved page |
| Documents – Color | Copy and Print – Documents, and Extract from Copy, colour | ink-coverage tiers (print-cost-pricing engine CSV) |
| Documents – B&W | Copy and Print – Documents, B&W ticked | **Pricing Options:** *Per Page* (per page × paper size, default) or *Ink Coverage* (its own tier matrix, `documents_bw_printCostMatrix.csv`; B&W pages are measured in grayscale) |
| ID / Booklet – Color | ID Cards and Booklets (both modes), colour | fixed price per page by option – `IDBookletColor.csv`: One Side ₱15 (ID) / ₱20 (Booklet), Back to Back ₱25 / ₱30, Multiple Pages n/a / ₱20 |
| ID / Booklet – B&W | the same options, B&W ticked | per page, per option |
| Additional Charges | extra per charged page (blank pages are free) | **Filter** ₱5 (Print mode with a filter applied), **Scan 600 dpi** ₱5, **Enlarge to 120% (color)** ₱5 |

Printed jobs are charged per page × copies. **Print mode – Documents** (Color or B&W) is costed **as soon as files are uploaded** (File Upload or drag & drop, with Cart ticked): each file is processed like for printing, its pages are priced (Color: ink coverage by the pricing engine; B&W: per page and paper size), and **one cart item per file** (× copies) is added. Changing copies or the pricing updates the amounts; changing paper size, filter, B&W or option re-runs the costing; Clear removes the items. Printing those files does not charge them a second time. 

### Ink-coverage pricing (print-cost-pricing engine)
Colour pages are priced by the bundled **print-cost-pricing** engine (`pricing-engine/scripts/pricing.py` and `price.py`, copied unchanged), so the app's prices match the shop's other pricing tools exactly. Each finished sheet is measured with the engine's rules (built into the program, see below):
- **Ink coverage** = share of the A4 sheet that isn't white (gray < 240), measured on the final sheet as printed.
- The coverage is looked up in a **cost matrix CSV** (engine format: Tier ID, Coverage Range (Min), Coverage Range (Max), Price (₱), Description). A tier covers Min ≤ coverage < Max; the last tier includes 100. Default tiers (from the bundled `printCostMatrix.csv`):

| Tier | Coverage | Price |
|---|---|---|
| tier-1 | 0 – 10% | ₱10 |
| tier-2 | 10 – 25% | ₱15 |
| tier-3 | 25 – 40% | ₱20 |
| tier-4 | 40 – 60% | ₱25 |
| tier-5 | 60 – 80% | ₱30 |
| tier-6 | 80 – 100% | ₱35 |

- **Blank pages** (≤ 0.05 % coverage) are **free and not printed** – in colour and B&W, in every option.
- The ink matrix is *Documents – Color* (Documents and Extract from Copy), edited in **Pricing matrix** and saved as `documents_color_printCostMatrix.csv` in `%AppData%\IdCopyStation\Pricing\` (starts as a copy of the bundled matrix). Tiers must be contiguous from 0 to 100. Change prices there – never in the engine code. ID cards and booklets use the fixed *ID / Booklet – Color* table (`IDBookletColor.csv`, same folder); the engine is still run on them to find blank pages, which are free and not printed.
- Additional charges are added on top of the tier price; copies multiply the page prices.

**No Python needed:** the engine's measuring rules (`pricing.py`) are built into the program in C#: render at 50 dpi in grayscale, a pixel is ink below gray level 240, coverage = inked share × share of the A4 sheet, ≤ 0.05 % = blank (free, not printed), tier min ≤ coverage < max. Only MuPDF's internal image shrinking is approximated (area averaging), so coverage can differ from the Python engine by a few hundredths of a percent – which only matters for a page right on a tier boundary.

**Check it once (optional):** on a PC that has Python + PyMuPDF, save some typical jobs as JPG with *Save to File*, then run
`powershell -ExecutionPolicy Bypass -File tools\verify-ink-engine.ps1 -Folder C:\path\to\jpgs`.
It prices every image with both engines and lists the coverage difference and any page whose tier or blank result differs.

## Account & licence
iScan&Print needs an account and a valid **7-day free trial** or **subscription** (Monthly ₱199, Annual ₱1,990). The licence server is the separate `license-server` project (Supabase).

- **At start-up** a small window checks the licence. With no saved sign-in, the **Account** window opens:
  - **Sign in** (email + password), **Create an account** (agree to the Terms; a code is emailed to confirm the address), and **Forgot password?** (a code is emailed; enter it with the new password).
  - The customer stays signed in; the session is stored encrypted for the Windows user (`%AppData%\IdCopyStation\License`).
- **The first check needs internet.** After that the app works offline until the licence's offline limit (7 days since the last check). It re-checks every 6 hours while running.
- **Locked:** when the trial or subscription has ended (or the PC clock was moved back, or the offline limit passed), **Copy, Print and Save to File** are disabled. Everything else still works (settings, cart, pricing).
- The header shows the status as a link – *Free trial – 5 days left*, *Monthly plan – active until Dec 23, 2026*, *Free trial ended – Subscribe* – which opens the right screen.
- **Subscribe** window: Monthly and Annual plans, how to pay (GCash, Maya, bank – details in `LicenseSettings.cs`), then the amount and **reference number** of the payment. It is sent to XIS Systems as *pending*; once approved (`admin.ps1 approve <id>`) the app unlocks at the next check, or at once with **Check now**. A rejected payment shows the reason.
- **Account in use on another PC:** one PC per account. The app lists the account's PCs and can **release** one (up to 3 moves in 30 days).
- **About** shows the account, plan, paid-until or trial-end date, days left, status and last online check, with **Subscribe…** / **Renew…** and **Sign out**.

**Before publishing**, fill in `LicenseSettings.cs`: the Supabase URL and anon key, the public key (`license-public-key.txt` from the license server), and your GCash / Maya / bank details and support contact. A **Release** build that still has the placeholders refuses to start; a **Debug** build (`dotnet run`) runs unlocked for development.

## Automatic updates
At start-up the app reads the website's signed update file (`LicenseSettings.UpdateManifestUrl`). When a newer
version exists it shows **Update now / Later** with the release notes (**Update now / Exit** for a required
update), downloads the new `iScanPrint.exe`, checks its signature and SHA-256, replaces itself and restarts.
*About → Check for updates* checks on demand. Only the published `iScanPrint.exe` updates itself, and its
folder must be writable. Publishing a release: see `website\README.md`.

## Save to File
A pop-up asks for the **folder** and the **type**:
- **PDF** – one file `Scan_yyyyMMdd_HHmmss.pdf`; multiple scans/pages become a multi-page PDF (each page keeps its paper size).
- **JPG** – one image per sheet: `Scan_yyyyMMdd_HHmmss.jpg`, or `…_01.jpg`, `…_02.jpg`… for multiple.

## Notes / limitations
- If only one scanner is installed it is selected at start-up so the ADF option can appear; otherwise it appears after **Select Scanner…** or the first scan.
- **Copies are 100 % of the original**: no layout margin and no padding around detected originals. Copy mode – Documents places the scan at its real size (reduced only if it is larger than the selected paper). Make sure the printer driver's own "Fit to page / Shrink" option is off. Content in the printer's unprintable edge (usually 3–5 mm) is not printed, but the image is not shrunk.
- Copy mode keeps originals the way they were placed (skew is removed); use the preview's rotate buttons if needed.
- Keep the scanner lid **closed** (white or black lids are both handled).
- Extract from Copy assumes an uploaded image shows a full A4/Letter page (used for real-world size).
- Print mode works best with the whole page visible and some contrast against the background. OCR orientation works best for languages installed in Windows (Settings → Time & language → Language).
- Copy mode and ID layouts print on A4; Documents use the selected paper size. If the printer driver has no matching paper, a custom size is requested.
- ID cards from photos have no real scale, so they are printed at standard ID-1 size (×1.2 when enlarged).
