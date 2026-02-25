# Thiết Lập Obsidian Mobile iOS: Sync Git Với Jekyll Blog

Di động iOS edit Obsidian mượt mà, sync Jekyll blog chỉ bằng plugin – không cần PC! Workflow dùng **Obsidian Git** native (đã hỗ trợ iOS từ v0.8+), kết nối GitHub repo Jekyll trực tiếp. Hoàn hảo cho on-the-go viết về Docker Ubuntu.[1][2][3]

## Kiến Thức Tiền Đề
- **Môi trường**: iOS 16+, Obsidian app (App Store, free), GitHub repo Jekyll (từ bài trước). App bổ sung: Working Copy (paid, $20 one-time) cho advanced sync.
- **Công cụ**: Obsidian Git plugin (free), Personal Access Token (PAT) GitHub. Không cần iSH/termux phức tạp.
- **Yêu cầu**: Vault local (không iCloud để tránh conflict), .obsidian folder customize mobile.[4][1]

## Phân Tích Hiện Trạng
Obsidian iOS mạnh edit Markdown/graph nhưng sync Git kém native trước đây. Giờ Obsidian Git work tốt trên mobile: auto-commit, manual pull/push. Tuy nhiên, conflict merge cần resolve thủ công, PAT expire nếu không classic/full.[5][1]

## Mục Tiêu
Sau setup, mở Obsidian iOS → edit `_posts/docker.md` → commit/push → Jekyll GitHub Pages rebuild tự động. Sync 2-way desktop-mobile seamless.

## Phân Tích Gap
- **Thách thức**: iOS sandboxed filesystem, plugin Git push fail nếu PAT sai, config desktop override mobile (workspace plugins).
- **Lỗi thường gặp**: "Authentication failed" (PAT scopes thiếu repo), merge conflict khi edit đồng thời, images không sync.[2][3]

## Giải Pháp Chi Tiết
**WHY?** Obsidian Git plugin handle toàn bộ: init repo, commit, push/pull ngay trong app. Native > Working Copy cho beginner.

### Bước 1: Prep GitHub Repo Trên Desktop
```
# Trên Ubuntu/PC
cd my-obsidian-blog  # Jekyll repo
git remote add origin https://github.com/yourusername/my-obsidian-blog.git
git push -u origin main
```
**WHY?** Repo ready với history.

### Bước 2: Tạo PAT GitHub
- GitHub > Settings > Developer settings > Personal access tokens > Tokens (classic) > Generate new.
- Scopes: `repo` (full control).
- Copy token (chỉ show 1 lần). **Cảnh báo ⚠️**: Lưu an toàn, như password manager.

### Bước 3: Setup Obsidian iOS
1. Download Obsidian từ App Store.
2. Tạo vault **local** (không "Open folder as vault from iCloud").
3. Files app > On My iPhone > Obsidian > [Vault] > Copy folder Jekyll repo từ PC (AirDrop/iCloud Drive).
   - Paste vào Obsidian folder, replace nếu có.
**WHY?** Vault = repo root, bao gồm `_posts`, `_config.yml`.

### Bước 4: Enable Obsidian Git Plugin
- Obsidian > Settings (⚙️) > Community plugins > Turn on > Browse > Tìm "Obsidian Git" > Install > Enable.
**WHY?** Plugin chính thức, support iOS push/pull.

### Bước 5: Config Plugin
```
Settings > Obsidian Git:
- Base path: . (root)
- Username: yourgithubusername
- Password / Token: [paste PAT]
- Author Name: Your Name
- Author Email: your@email.com
- Auto commit interval: 5 minutes (optional)
```
- Command Palette (swipe right hoặc ⌘P): "Obsidian Git: Clone existing remote repo" nếu cần (url: https://github.com/yourusername/my-obsidian-blog.git).
**WHY?** Auth HTTPS, tránh SSH phức tạp iOS.

### Bước 6: Test Sync
1. Edit note `_drafts/test.md` trên iOS.
2. Command Palette: `Obsidian Git: Create commit` > Message: "iOS edit".
3. `Obsidian Git: Push` → Success!
4. Pull trên desktop: `git pull` → Changes sync.
**WHY?** Trigger GitHub Pages rebuild.

**Troubleshooting**:
- Push fail: Check PAT expire/regenerate, scopes `repo`.
- Plugin không load: Restart app, check .obsidian/mobile folder riêng (Settings > About > Override config folder: .obsidian.mobile).
- Conflict: `Obsidian Git: Pull before push`, resolve Markdown thủ công.[1][2]

**Pro tip với Working Copy (nếu paid)**:
- Install Working Copy > Clone repo > Share > Setup Folder Sync > Obsidian vault folder.
- Auto pull/push background. **WHY?** Seamless hơn plugin cho large vault.

## Best Practices
- **Separate config**: .obsidian.mobile cho iOS (tránh desktop plugins crash mobile).
- **Auto commit**: 5-10min, nhưng manual push trước edit desktop.
- **Backup**: iCloud Drive vault copy, PAT rotate 90 ngày.
- **Kinh nghiệm**: Test small vault trước. GitHub Publisher plugin nếu chỉ publish posts (không full sync).[4][1]

## Bước Tiếp Theo
- Plugins thêm: Templater (snippets YAML), Advanced Tables (edit table mobile).
- Advanced: Shortcuts app + a-Shell cho git CLI.
- Resources: Obsidian Forum "iOS Git sync", denolehov/obsidian-git GitHub issues.
- Next: Android sync tương tự (mGit F-Droid).

## Kết Luận
Với Obsidian Git trên iOS, bạn edit Jekyll blog mọi nơi – push 1 lệnh, site live ngay! Setup 10p, dùng mãi mãi. Mobile blogging unlocked! 📱✨[3][2][1]

Nguồn
[1] [Mobile] Setting up iOS git-based syncing with mobile app (using ... https://forum.obsidian.md/t/mobile-setting-up-ios-git-based-syncing-with-mobile-app-using-working-copy/16499
[2] [Obsidian Git] Sync on your ios without any extra app https://forum.obsidian.md/t/obsidian-git-sync-on-your-ios-without-any-extra-app/60639
[3] Obsidian Git on iOS https://www.codybontecou.com/obsidian-git-on-ios
[4] How to Sync Obsidian Across All Your Devices (Including Free ... https://www.stephanmiller.com/sync-obsidian-vault-across-devices/
[5] Add mobile installation instructions · Issue #305 https://github.com/denolehov/obsidian-git/issues/305
[6] Does anyone use git and android with obsidian? - Reddit https://www.reddit.com/r/ObsidianMD/comments/oimbiy/does_anyone_use_git_and_android_with_obsidian/
[7] iPhone(iPad)でGitHubのObsidian Vaultを同期する - Zenn https://zenn.dev/k1nsenka/articles/cec6bb05b33f36
[8] My Git backup workflow on iOS (better than Working Copy) https://forum.obsidian.md/t/my-git-backup-workflow-on-ios-better-than-working-copy/52966
[9] Obsidian + Android + Syncing via GitHub in 2023 https://www.reddit.com/r/ObsidianMD/comments/17odzjb/obsidian_android_syncing_via_github_in_2023/
[10] Is it possible to use Git with Obsidian mobile? https://www.reddit.com/r/ObsidianMD/comments/ojclli/is_it_possible_to_use_git_with_obsidian_mobile/
[11] スマホ側でインストール https://zenn.dev/ishikawa096/articles/158246fc5a5d62
[12] Setting Up Obsidian Git on iOS without iSH* or Working Copy https://forum.obsidian.md/t/setting-up-obsidian-git-on-ios-without-ish-or-working-copy/97800
[13] How to Sync Obsidian Notes for FREE on iPhone, iPad, and ... https://www.youtube.com/watch?v=JcaEeE51F-0
[14] ObsidianをWindows/macOS/iOSでGit (GitHub) - rcmdnk's blog https://rcmdnk.com/blog/2025/02/24/computer-obsidian/
[15] Obsidian：iCloudとGitHubの安全な併用マニュアル - note https://note.com/sewasees/n/ne145f7fda059
