const express = require('express');
const qrcode = require('qrcode');
const {
    default: makeWASocket,
    useMultiFileAuthState,
    fetchLatestBaileysVersion,
    DisconnectReason,
} = require('@whiskeysockets/baileys');

const app = express();
const PORT = process.env.PORT || 3000;
const AUTH_DIR = process.env.AUTH_DIR || './auth_info';

let botStatus = 'Starting WhatsApp connection…';
let qrCodeImage = null;
let reconnectTimer;

app.get('/', (_req, res) => {
    res.type('html').send(`<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>WhatsApp Bot</title>
    <style>
        body {
            margin: 0;
            min-height: 100vh;
            display: grid;
            place-items: center;
            background: #111b21;
            color: #fff;
            font-family: system-ui, sans-serif;
        }

        main {
            width: min(420px, calc(100% - 32px));
            box-sizing: border-box;
            padding: 24px;
            text-align: center;
            background: #202c33;
            border-radius: 14px;
        }

        #status {
            color: #00a884;
            font-weight: 700;
        }

        #qr {
            width: 280px;
            max-width: 100%;
            min-height: 280px;
            border-radius: 8px;
            background: white;
        }

        #qr.hidden {
            display: none;
        }
    </style>
</head>
<body>
    <main>
        <h1>WhatsApp Bot</h1>
        <p id="status">Loading…</p>

        <img id="qr" alt="WhatsApp QR code">

        <p>
            WhatsApp → Linked devices → Link a device
        </p>
    </main>

    <script>
        async function refresh() {
            try {
                const response = await fetch('/status');
                const data = await response.json();

                document.getElementById('status').textContent = data.status;

                cons
