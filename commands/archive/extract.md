# Extract Slack Links

**STATUS: ARCHIVED** — Used <3 times in 73 sessions. Specialized use case with external dependency. Invoke manually when needed.

Extract papers, links, and content shared in Slack channels and output to CSV.

## Interaction

Ask the user:

**"How many days of Slack messages do you want to extract?"**

Provide these options:
- **7 days** — Last week
- **30 days** — Last month
- **90 days** — Last quarter
- **180 days** — Last 6 months
- **Custom** — Let user specify

## Execution

Once the user specifies the number of days:

1. Navigate to the slack-extractor folder:
   ```bash
   cd "/Users/vivekkhandelwal/Desktop/Claude code/GetCogniSwitch/slack-extractor"
   ```

2. Run the extraction script with the days parameter:
   ```bash
   node extract.js --days=<NUMBER>
   ```

3. Show the summary output to the user including:
   - Total links found
   - Breakdown by category (arxiv, linkedin, github, twitter, youtube, blog/other)
   - Location of the CSV file

## Output

The CSV will be saved in the slack-extractor folder with the name:
`slack-links-YYYY-MM-DD.csv`

Columns:
- date
- channel
- category
- url
- posted_by
- message_preview

## Channels Being Extracted

- CR92K7YUB (dev-ml-graph)
- C07PKNA191A (ai-agents-apps)
- C08SFKKK189
- C0676GG96LV (gen_ai)

## Troubleshooting

If extraction fails:
- Check that `.env` file exists in slack-extractor folder
- Verify SLACK_TOKEN is valid (starts with `xoxp-`)
- Confirm you have access to the channels being extracted
