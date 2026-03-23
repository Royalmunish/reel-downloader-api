from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import yt_dlp

app = FastAPI()

class ReelRequest(BaseModel):
    url: str

@app.post("/download")
def download_reel(request: ReelRequest):
    ydl_opts = {'format': 'best', 'quiet': True}
    try:
        with yt_dlp.YoutubeDL(ydl_opts) as ydl:
            info = ydl.extract_info(request.url, download=False)
            return {"status": "success", "download_url": info.get('url')}
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))
