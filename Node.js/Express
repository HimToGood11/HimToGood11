// server.js - Deploy as single file
const express = require('express');
const axios = require('axios');
const cors = require('cors');
const app = express();
app.use(cors());
app.use(express.json());
app.use(express.static('public'));

// Discord OSINT endpoints
app.post('/api/discord/:id', async (req, res) => {
    const { id, email } = req.body;
    try {
        const data = {
            user: await axios.get(`https://discord.com/api/v10/users/${id}`, {
            headers: { 'User-Agent': 'Mozilla/5.0' }
        }).catch(() => null),
        guilds: await axios.get(`https://discord.com/api/v10/users/${id}/guilds`, {
            headers: { 'User-Agent': 'Mozilla/5.0' }
        }).catch(() => []),
        profile: `https://discord.com/users/${id}`
    };
        // Email recon (basic)
        const emailData = email ? {
            haveibeenpwned: `https://haveibeenpwned.com/api/v3/breachedaccount/${email}?truncateResponse=true`
        } : {};
        
        res.json({ ...data, ...emailData, status: 'success' });
    } catch (e) {
        res.json({ error: 'User private or ID invalid', status: 'error' });
    }
});

// Roblox OSINT
app.post('/api/roblox/:id', async (req, res) => {
    const { id } = req.body;
    try {
        const profile = await axios.get(`https://users.roblox.com/v1/users/${id}`);
        const friends = await axios.get(`https://friends.roblox.com/v1/users/${id}/friends/count`);
        const badges = await axios.get(`https://badges.roblox.com/v1/users/${id}/badges/all`);
        
        res.json({
            profile: profile.data,
            friendsCount: friends.data.count,
            badges: badges.data.data.slice(0,10),
            games: `https://games.roblox.com/v2/users/${id}/games/favorites`
        });
    } catch {
        res.json({ error: 'Roblox ID invalid', status: 'error' });
    }
});

app.listen(process.env.PORT || 3000);