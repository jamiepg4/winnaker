
## Notes from update to the new code 6/29/2017

MikeR had this commit on 4/25/2017:
```
    Match against a div instead of "execution" element (#3)

    The latest update has removed the execution element, so we can't use
    that as the root of the match when looking for builds any more. There's
    a div with the execution class that's in both the old and new UIs
    though, so match against that. The logic is a bit funky, cause we want
    to match against divs with exactly the "execution" class, and there are
    lots of classes that contain execution as a part. So we need to do a
    slightly funky match to make sure we only hit exactly the execution
    class.
    1 file changed, 4 insertions(+), 4 deletions(-)
    winnaker/models.py | 8 ++++----

    modified   winnaker/models.py
    @@ -149,16 +149,16 @@ class Spinnaker():
                     sys.exit(2)

         def get_last_build(self):
    -        execution_summary_xp = "//execution[1]//div[@class='execution-summary']"
    +        execution_summary_xp = "//div[contains(concat(' ',@class,' '), ' execution ')][1]//div[@class='execution-summary']"
             execution_summary = wait_for_xpath_presence(
                 self.driver, execution_summary_xp)

    -        trigger_details_xp = "//execution[1]//ul[@class='trigger-details']"
    +        trigger_details_xp = "//div[contains(concat(' ',@class,' '), ' execution ')][1]//ul[@class='trigger-details']"
             trigger_details = wait_for_xpath_presence(
                 self.driver, trigger_details_xp)
             self.build = Build(trigger_details.text, execution_summary.text)
             time.sleep(1)
    -        detail_xpath = "//execution[1]//execution-status//div/a[contains(., 'Details')]"
    +        detail_xpath = "//div[contains(concat(' ',@class,' '), ' execution ')][1]//execution-status//div/a[contains(., 'Details')]"
             e = wait_for_xpath_presence(self.driver, detail_xpath)
             self.driver.save_screenshot("./outputs/last_build_status.png")
             return self.build
    @@ -166,7 +166,7 @@ class Spinnaker():
         def get_stages(self, n=2):
             # n number of stages to get
             for i in range(1, n + 1):
    -            stage_xpath = "//execution[1]//div[@class='stages']/div[" + str(
    +            stage_xpath = "//div[contains(concat(' ',@class,' '), ' execution ')][1]//div[@class='stages']/div[" + str(
                     i) + "]"
                 e = wait_for_xpath_presence(
                     self.driver, stage_xpath, be_clickable=True)
```

Three of these xpath expressions have moved into configuration -

```
    cfg_execution_summary_xp
    cfg_trigger_details_xp
    cfg_details_xpath
```

and the last, in get_stages() has been rewritten as:

```
    stage_xpath = "//execution-groups[1]//div[@class='stages']/div[" + str(
```

For now, leave these out. We will re-evaluate to see if we still need to patch the value of stage_xpath.
