import http.server
import socketserver
import json
import os
import shutil
import subprocess
import tempfile
import urllib.parse
import webbrowser
from pathlib import Path

PORT = 8080

HTML_UI = """<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Termux Git Force-Sync</title>
    <style>
        :root {
            --bg: #0f172a;
            --card: #1e293b;
            --text: #f8fafc;
            --muted: #94a3b8;
            --accent: #3b82f6;
            --danger: #ef4444;
            --border: #334155;
        }
        * { box-sizing: border-box; font-family: system-ui, -apple-system, sans-serif; }
        body {
            background: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 16px;
            display: flex;
            justify-content: center;
        }
        .container {
            width: 100%;
            max-width: 540px;
            background: var(--card);
            padding: 20px;
            border-radius: 12px;
            border: 1px solid var(--border);
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
        }
        h2 { margin-top: 0; font-size: 1.3rem; display: flex; align-items: center; gap: 8px; }
        .badge { background: var(--danger); font-size: 0.7rem; padding: 3px 8px; border-radius: 99px; text-transform: uppercase; }
        label { display: block; margin-top: 14px; margin-bottom: 6px; font-size: 0.85rem; color: var(--muted); font-weight: 600; }
        input, textarea {
            width: 100%;
            padding: 10px 12px;
            background: var(--bg);
            border: 1px solid var(--border);
            border-radius: 8px;
            color: var(--text);
            font-size: 0.95rem;
        }
        input:focus, textarea:focus { outline: none; border-color: var(--accent); }
        textarea { resize: vertical; min-height: 70px; font-family: monospace; font-size: 0.85rem; }
        .row { display: flex; gap: 10px; }
        .row > div { flex: 1; }
        button {
            width: 100%;
            margin-top: 20px;
            padding: 14px;
            background: var(--danger);
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            transition: opacity 0.2s;
        }
        button:disabled { opacity: 0.5; cursor: not-allowed; }
        #logs {
            margin-top: 18px;
            background: #020617;
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 12px;
            font-family: monospace;
            font-size: 0.8rem;
            white-space: pre-wrap;
            word-break: break-all;
            max-height: 260px;
            overflow-y: auto;
            color: #38bdf8;
            display: none;
        }
        .hint { font-size: 0.75rem; color: var(--muted); margin-top: 4px; }
    </style>
</head>
<body>
<div class="container">
    <h2>Termux Git Overwrite <span class="badge">Force Sync</span></h2>

    <label>GitHub Repository URL</label>
    <input type="text" id="repoUrl" placeholder="https://github.com/username/repo.git">

    <label>GitHub Personal Access Token (PAT)</label>
    <input type="password" id="patToken" placeholder="ghp_xxxxxxxxxxxxxxxxxxxx">

    <div class="row">
        <div style="flex: 2;">
            <label>Project Folder Path (in Termux)</label>
            <input type="text" id="projectPath" placeholder="/sdcard/my-project or ~/app">
        </div>
        <div style="flex: 1;">
            <label>Target Branch</label>
            <input type="text" id="branch" value="main">
        </div>
    </div>

    <label>Excluded Folders & Files (Comma-separated)</label>
    <textarea id="excludes">.git, .gitignore, node_modules, .next, .nextjs, .vercel, dist, build, __pycache__, .env</textarea>
    <div class="hint">These items are skipped during staging. Your original project folder is never modified.</div>

    <button id="pushBtn" onclick="startPush()">FORCE REPLACE REPO</button>

    <div id="logs"></div>
</div>

<script>
    // Load saved settings from localStorage (excluding PAT for basic safety, or keep if preferred)
    window.onload = () => {
        if(localStorage.getItem('repoUrl')) document.getElementById('repoUrl').value = localStorage.getItem('repoUrl');
        if(localStorage.getItem('projectPath')) document.getElementById('projectPath').value = localStorage.getItem('projectPath');
        if(localStorage.getItem('branch')) document.getElementById('branch').value = localStorage.getItem('branch');
        if(localStorage.getItem('excludes')) document.getElementById('excludes').value = localStorage.getItem('excludes');
    };

    async function startPush() {
        const repoUrl = document.getElementById('repoUrl').value.trim();
        const patToken = document.getElementById('patToken').value.trim();
        const projectPath = document.getElementById('projectPath').value.trim();
        const branch = document.getElementById('branch').value.trim() || 'main';
        const excludes = document.getElementById('excludes').value;

        if (!repoUrl || !patToken || !projectPath) {
            alert("Please fill in Repo URL, PAT Token, and Project Path.");
            return;
        }

        if (!confirm(`WARNING: This will completely overwrite the '${branch}' branch on GitHub with the contents of:\n${projectPath}\n\nProceed?`)) {
            return;
        }

        // Save non-sensitive preferences
        localStorage.setItem('repoUrl', repoUrl);
        localStorage.setItem('projectPath', projectPath);
        localStorage.setItem('branch', branch);
        localStorage.setItem('excludes', excludes);

        const btn = document.getElementById('pushBtn');
        const logs = document.getElementById('logs');
        btn.disabled = true;
        btn.innerText = "SYNCING & PUSHING...";
        logs.style.display = "block";
        logs.style.color = "#38bdf8";
        logs.innerText = "Starting process in Termux...\n";

        try {
            const res = await fetch('/push', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ repoUrl, patToken, projectPath, branch, excludes })
            });
            const data = await res.json();
            logs.innerText = data.logs;
            logs.style.color = data.success ? "#4ade80" : "#f87171";
        } catch (err) {
            logs.innerText = "Network/Server Error: " + err.message;
            logs.style.color = "#f87171";
        } finally {
            btn.disabled = false;
            btn.innerText = "FORCE REPLACE REPO";
        }
    }
</script>
</body>
</html>
"""

def run_cmd(cmd, cwd, pat_token=None):
    result = subprocess.run(
        cmd,
        cwd=cwd,
        stdout=subprocess.PIPE,
        stderr=subprocess.PIPE,
        text=True
    )
    out = (result.stdout + "\n" + result.stderr).strip()
    # Mask PAT token in logs so it never leaks to the UI
    if pat_token and pat_token in out:
        out = out.replace(pat_token, "***TOKEN***")
    return result.returncode, out

class GitSyncHandler(http.server.BaseHTTPRequestHandler):
    def do_GET(self):
        if self.path == '/':
            self.send_response(200)
            self.send_header('Content-type', 'text/html; charset=utf-8')
            self.end_headers()
            self.wfile.write(HTML_UI.encode('utf-8'))
        else:
            self.send_error(404)

    def do_POST(self):
        if self.path != '/push':
            self.send_error(404)
            return

        content_length = int(self.headers['Content-Length'])
        post_data = json.loads(self.rfile.read(content_length).decode('utf-8'))

        repo_url = post_data.get('repoUrl', '').strip()
        pat_token = post_data.get('patToken', '').strip()
        raw_path = post_data.get('projectPath', '').strip()
        branch = post_data.get('branch', 'main').strip()
        excludes_raw = post_data.get('excludes', '')

        # Expand ~ to Termux home path
        project_path = os.path.expanduser(raw_path)
        exclude_set = {item.strip() for item in excludes_raw.split(',') if item.strip()}

        logs = []
        success = False

        try:
            if not os.path.exists(project_path) or not os.path.isdir(project_path):
                raise Exception(f"Directory does not exist: {project_path}")

            # Build authenticated URL safely
            parsed = urllib.parse.urlparse(repo_url)
            if parsed.scheme not in ('http', 'https'):
                raise Exception("Repo URL must start with https://")
            
            clean_netloc = parsed.netloc.split('@')[-1]
            auth_url = f"{parsed.scheme}://{pat_token}@{clean_netloc}{parsed.path}"

            logs.append(f"[1/5] Scanning source: {project_path}")
            logs.append(f"      Ignoring: {', '.join(sorted(exclude_set))}")

            # Create an isolated temporary directory
            with tempfile.TemporaryDirectory(prefix="termux_gitsync_") as temp_dir:
                
                # Custom copy function to skip excluded folders/files at every depth
                def ignore_patterns(dir_path, names):
                    ignored = set()
                    for name in names:
                        if name in exclude_set:
                            ignored.add(name)
                    return ignored

                logs.append("[2/5] Copying clean files to isolated staging area...")
                for item in os.listdir(project_path):
                    if item in exclude_set:
                        continue
                    src = os.path.join(project_path, item)
                    dst = os.path.join(temp_dir, item)
                    if os.path.isdir(src):
                        shutil.copytree(src, dst, ignore=ignore_patterns, symlinks=True)
                    else:
                        shutil.copy2(src, dst)

                if not os.listdir(temp_dir):
                    raise Exception("Staging folder is empty! Check your path or exclusion list.")

                logs.append("[3/5] Initializing fresh orphan Git repository...")
                commands = [
                    (["git", "init"], "git init"),
                    (["git", "config", "user.name", "Termux Sync"], "git config name"),
                    (["git", "config", "user.email", "sync@termux.local"], "git config email"),
                    (["git", "checkout", "-b", branch], f"git checkout -b {branch}"),
                    (["git", "add", "-A"], "git add -A"),
                    (["git", "commit", "-m", "Full repository sync via Termux"], "git commit"),
                ]

                for cmd, label in commands:
                    code, out = run_cmd(cmd, temp_dir, pat_token)
                    if code != 0:
                        raise Exception(f"Failed at '{label}':\n{out}")

                logs.append(f"[4/5] Force-pushing to origin/{branch}...")
                code, out = run_cmd(["git", "push", "--force", auth_url, branch], temp_dir, pat_token)
                if code != 0:
                    raise Exception(f"Push failed:\n{out}")

                logs.append(f"[5/5] Done! Remote repository completely replaced.\n\nGit Output:\n{out}")
                success = True

        except Exception as e:
            logs.append(f"\n[ERROR] {str(e)}")
            success = False

        self.send_response(200)
        self.send_header('Content-type', 'application/json')
        self.end_headers()
        response = json.dumps({"success": success, "logs": "\n".join(logs)})
        self.wfile.write(response.encode('utf-8'))

    # Silence default terminal HTTP request noise
    def log_message(self, format, *args):
        pass

if __name__ == '__main__':
    socketserver.TCPServer.allow_reuse_address = True
    with socketserver.TCPServer(("127.0.0.1", PORT), GitSyncHandler) as httpd:
        url = f"http://127.0.0.1:{PORT}"
        print(f"==========================================")
        print(f" Termux Git Force-Sync UI is running!")
        print(f" Open in browser: {url}")
        print(f" Press Ctrl+C to stop.")
        print(f"==========================================")
        # Attempt to auto-open Android browser via termux-open-url if available
        os.system(f"termux-open-url {url} 2>/dev/null || true")
        try:
            httpd.serve_forever()
        except KeyboardInterrupt:
            print("\nServer stopped.")
