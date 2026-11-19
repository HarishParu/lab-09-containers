# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

One reason this would be a bad idea is that anyone would be able to read the credentials because  anyone that pulled the image would be able to see my credentials and acess private information in my AWS account. The second reason is that the credentials would expire after the 4 hour period anyway, so when the lab expires, no one would be able to access the image.

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

Without the -e options, MONGODB_ATLAS_URL was not set in the container, so get_atlas_settings() skipped the environment variables. It read the SSM parameter mongodb_credentials with boto3 instead, using the AWS credentials from my ~/.aws, which was in /home/app/.aws. The parameter’s JSON value supplied the url, user, and password that connect() used to log in to Atlas.

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

Before: 9b0f72865040   After: df9cbf0d2ec0

It's a problem for someone who uses my image that 0.1.0 names a different image because if someone tried to pull the image before I changed it, and someone tried to pull it after, they're would be a discrepancy in the results they got. If one person cited the image before the rebuild and one did after, they're each citing an image of the same label that is evidently different. To fix this, I should have changed the label for the second image to something else like 0.1.1 so they would have two distinct labels.
