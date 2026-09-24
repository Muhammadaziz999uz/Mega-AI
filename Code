const express = require("express");
const cors = require("cors");
require("dotenv").config();

const app = express();
app.use(cors());
app.use(express.json());

const RUNWAY_API_KEY = process.env.RUNWAY_API_KEY;
const RUNWAY_VERSION = "2024-11-06";
const RUNWAY_BASE = "https://api.dev.runwayml.com/v1";

if (!RUNWAY_API_KEY) {
    console.warn("OGOHLANTIRISH: RUNWAY_API_KEY .env faylida topilmadi!");
}

app.post("/generate-video", async (req, res) => {
    const { prompt, imageUrl } = req.body;

    if (!prompt) {
        return res.status(400).json({ error: "prompt majburiy" });
    }

    try {
        const body = {
            model: "gen4_turbo",
            promptText: prompt,
            promptImage: imageUrl || "https://picsum.photos/1280/768"
        };

        const runwayRes = await fetch(`${RUNWAY_BASE}/image_to_video`, {
            method: "POST",
            headers: {
                "Authorization": `Bearer ${RUNWAY_API_KEY}`,
                "X-Runway-Version": RUNWAY_VERSION,
                "Content-Type": "application/json"
            },
            body: JSON.stringify(body)
        });

        const data = await runwayRes.json();

        if (!runwayRes.ok) {
            console.error("Runway xatolik:", data);
            return res.status(runwayRes.status).json({
                error: data?.error || "Runway so'rovi muvaffaqiyatsiz"
            });
        }

        res.json({ taskId: data.id });

    } catch (err) {
        console.error(err);
        res.status(500).json({ error: "Server xatoligi: " + err.message });
    }
});

app.get("/video-status/:taskId", async (req, res) => {
    const { taskId } = req.params;

    try {
        const runwayRes = await fetch(`${RUNWAY_BASE}/tasks/${taskId}`, {
            headers: {
                "Authorization": `Bearer ${RUNWAY_API_KEY}`,
                "X-Runway-Version": RUNWAY_VERSION
            }
        });

        const data = await runwayRes.json();

        if (!runwayRes.ok) {
            return res.status(runwayRes.status).json({
                error: data?.error || "Holatni olishda xatolik"
            });
        }

        res.json({
            status: data.status,
            videoUrl: data.output ? data.output[0] : null,
            failure: data.failure || null
        });

    } catch (err) {
        console.error(err);
        res.status(500).json({ error: "Server xatoligi: " + err.message });
    }
});

app.get("/", (req, res) => {
    res.send("MEGA AI video backend ishlamoqda ✅");
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server ${PORT}-portda ishlamoqda`);
});
