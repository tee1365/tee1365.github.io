---
title: flicket-problem
date: 2022-01-06 15:07:11
tags:
---

## webhook receive duplicate events

  problem: keep receiving the same events

  solution: add `response.status(200).send('EVENT_RECEIVED');` in the controller

  ```ts
    public webhook(@Body() body: WebhookMention, @Res() response: Response): void {
      switch (body.object) {
        case 'instagram':
          if ('changes' in body.entry[0]) {
            const commentMention = body.entry[0].changes[0];
            this.handleCommentMention();
            console.log(commentMention);
          } else if ('messaging' in body.entry[0]) {
            const storyMention = body.entry[0].messaging[0];
            this.handleStoryMention(storyMention);
            console.log(storyMention);
          }
      }
      response.status(200).send('EVENT_RECEIVED');
    }
  ```
