Fetch and display the current local weather.

Use the Bash tool to run:
```
curl -s "wttr.in?format=v2"
```

Then present the output in a clean, readable summary covering:
- Current temperature (°C and °F)
- Conditions (sunny, cloudy, rain, etc.)
- Feels-like temperature
- Wind speed and direction
- Humidity
- Today's high/low
- A brief forecast for the next 2 days if available

If `curl` fails (no network, service down), say so clearly and suggest the user check https://wttr.in in their browser.

Do not ask for a location — wttr.in auto-detects from the user's IP.
