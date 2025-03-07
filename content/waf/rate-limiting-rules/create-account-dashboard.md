---
pcx_content_type: how-to
type: overview
title: Create in the dashboard for an account
weight: 15
meta:
  title: Create a rate limiting rule in the dashboard for an account
---

# Create a rate limiting rule in the dashboard for an account

At the account level, you must first create a custom rate limiting ruleset, containing one or more rate limiting rules, and then deploy it to one or more zones on an Enterprise plan.

{{<Aside type="note" header="Notes">}}
* Account-level rate limiting configuration requires an Enterprise plan with a paid add-on.
* At the API level, custom rate limiting rulesets are regular [custom rulesets](/waf/custom-rules/custom-rulesets/) with one or more rate limiting rules. The concept of custom rate limiting ruleset exists only in the Cloudflare dashboard to make it clear that you are configuring and deploying rate limiting rules at the account level.
{{</Aside>}}

## 1. Create a custom rate limiting ruleset

To create a new custom rate limiting ruleset:

1. Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/), and select your account.

2. Go to Account Home > **WAF** > **Rate limiting rulesets**.

3. Under **Your custom rate limiting rulesets**, select **Create new ruleset**.

4. Enter a name for the ruleset and (optionally) a description.

5. In the ruleset creation page, select **Create rule**.

6. In the rule creation page, enter a descriptive name for the rule in **Rule name**.

    ![Create rate limiting rule at the account level in the Cloudflare dashboard](/images/waf/custom-rules/rate-limiting-create-account.png)

7. Under **When incoming requests match**, use the **Field** drop-down list to choose an HTTP property. For each request, the value of the property you choose for **Field** is compared to the value you specify for **Value** using the operator selected in **Operator**.

8. (Optional) Under **Cache status**, disable **Also apply rate limiting to cached assets** to consider only the requests that reach the origin when determining the rate.

9. Under **With the same characteristics**, configure the characteristics that will define the request counters for rate limiting purposes. Each value combination will have its own counter to determine the rate. Refer to [How Cloudflare determines the request rate](/waf/rate-limiting-rules/request-rate/) for more information.

    The available characteristics depend on your Cloudflare plan and product subscriptions.

10. (Optional) To define an expression that specifies the conditions for incrementing the rate counter, enable **Use custom counting expression** and set the expression. By default, the counting expression is the same as the rule expression. The counting expression can include [response fields](/ruleset-engine/rules-language/fields/#http-response-fields).

11. Under **When rate exceeds**, define the maximum number of requests and the time period to consider when determining the rate.

12. Under **Then take action**, select the rule action from the **Choose an action** drop-down list. For example, selecting _Block_ tells Cloudflare to refuse requests in the conditions you specified when the request limit is reached.

13. (Optional) If you selected the _Block_ action, you can [configure a custom response](#configuring-a-custom-response-for-blocked-requests) for requests exceeding the configured rate limit.

14. Select the mitigation timeout in the **Duration** dropdown. This is the time period during which Cloudflare applies the select action once the rate is reached.

    Enterprise customers with a paid add-on can [throttle requests](/waf/rate-limiting-rules/parameters/#with-the-following-behavior) instead of applying the configured action for a selected duration. To throttle requests, under **With the following behavior** select _Throttle requests over the maximum configured rate_.

15. Select **Add rule**.

16. Create additional rate limiting rules as needed, and then select **Create** to create the ruleset.

## 2. Deploy the custom rate limiting ruleset

To deploy a custom rate limiting ruleset to one or more zones on an Enterprise plan:

1. Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/), and select your account.

2. Go to Account Home > **WAF** > **Rate limiting rulesets**.

3. Under **Your custom rate limiting rulesets** and next to the rate limiting ruleset you wish to deploy, select **Deploy**.

4. In the ruleset deployment page, enter a descriptive name for the rule deploying the ruleset in **Execution name**.

5. Under **Execution scope**, review the scope of the rate limiting ruleset to deploy. If necessary, select **Edit scope** and configure the expression that will determine the scope of the current rule.

    {{<Aside type="warning">}}
Deployed custom rate limiting rulesets will only apply to incoming traffic of zones on an Enterprise plan. The Expression Builder will automatically include this filter. If you define a custom expression using the Expression Editor, you must include `AND zone.level eq "ENT"` in your expression so that the rule only applies to zones on an Enterprise plan.
    {{</Aside>}}

6. To deploy your rule immediately, select **Deploy**. If you are not ready to deploy your rule, select **Save as Draft**.

The **Deployed custom rate limiting rulesets** list will show a rule for each deployed custom rate limiting ruleset.

## Configuring a custom response for blocked requests

{{<render file="_custom-response-blocked-requests.md">}}
