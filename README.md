# henry-chitanda
![image alt](https://github.com/Chitanda22/henry-chitanda/blob/e080c80030ddf8eff02f60406781f80211eca42b/WhatsApp%20Image%202026-07-18%20at%209.25.34%20PM.jpeg)
import requests
import base64

# --- Configuration ---
TOKEN = "your_personal_access_token"
OWNER = "your-username"
REPO = "your-repo-name"
FILE_PATH = "notes/hello.txt"   # path inside the repo
BRANCH = "main"

def write_to_github(content_text, commit_message="Update file"):
    url = f"https://api.github.com/repos/{OWNER}/{REPO}/contents/{FILE_PATH}"
    headers = {
        "Authorization": f"Bearer {TOKEN}",
        "Accept": "application/vnd.github+json"
    }

    # Check if file already exists (need its SHA to update instead of create)
    get_resp = requests.get(url, headers=headers, params={"ref": BRANCH})
    sha = get_resp.json().get("sha") if get_resp.status_code == 200 else None

    encoded_content = base64.b64encode(content_text.encode("utf-8")).decode("utf-8")

    payload = {
        "message": commit_message,
        "content": encoded_content,
        "branch": BRANCH
    }
    if sha:
        payload["sha"] = sha  # required if updating an existing file

    resp = requests.put(url, headers=headers, json=payload)

    if resp.status_code in (200, 201):
        print("Success:", resp.json()["content"]["html_url"])
    else:
        print("Failed:", resp.status_code, resp.text)

# Example usage
write_to_github("Hello from Python!\nThis is my note.", "Add hello note")
