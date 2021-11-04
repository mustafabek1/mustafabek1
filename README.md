   ### 👋 Hello! I'm Mustafa. 
                                                                  

name: Daily updates

on:
  schedule:
    # Once a day at 8 AM
    - cron: 0 8 * * *
  push:
    branches:
      - master

jobs:
  updates:
    name: Updates
    runs-on: ubuntu-latest
    steps:
      - name: Update this repo's README
        uses: JasonEtco/rss-to-readme@v1
        with:
          feed-url: https://jasonet.co/rss.xml
          readme-section: posts
          empty-commits: 'false'

      - name: Update my status
        uses: actions/github-script@v2
        with:
          github-token: ${{ secrets.GH_PAT }}
          script: |
            const query = `mutation changeUserStatus ($input: ChangeUserStatusInput!) {
              changeUserStatus (input: $input) {
                status {
                  emoji
                  message
                }
              }
            }`
            const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']
            const dayIndex = (new Date()).getDay()
            github.graphql(query, {
              input: {
                emoji: '👋',
                message: `Hello, happy ${days[dayIndex]}!`
              }
            })
