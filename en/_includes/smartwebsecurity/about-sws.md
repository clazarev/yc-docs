{{ sws-name }} protects your infrastructure against cybersecurity threats at OSI application layer (L7). These may include DDoS attacks, bots, and SQL injections. In addition, you can enable DDoS protection at L3 and L4 using [{{ ddos-protection-full-name }}](../../vpc/ddos-protection/index.md).

{{ sws-name }} is a toolkit to protect infrastructures of various complexity and scale. Protection is achieved by cleaning malicious traffic from the incoming traffic flow. The traffic is checked against filtering rules in a security profile. You can additionally process the cleaned traffic with ARL profile rules to reduce the load on your application.

A security profile may include:

* Basic rules for simple traffic filtering based on specified conditions.
* Smart Protection rules for automatic protection against DDoS attacks with machine learning and behavior analysis algorithms.
* WAF profile rules for protection against web app or website vulnerability exploits. They block many known threats, such as SQL and command injections, cross-site scripting, and more. You can add multiple rule sets to a WAF profile, e.g., [OWASP CRS](https://owasp.org/www-project-modsecurity-core-rule-set/), [Yandex Ruleset](../../smartwebsecurity/concepts/waf.md#yandex-ruleset), and [ML WAF (Yandex Malicious Score)](../../smartwebsecurity/concepts/waf.md#yandex-ml-ruleset).
* Built-in [{{ captcha-full-name }}](../../smartcaptcha/) to run [CAPTCHA](https://en.wikipedia.org/wiki/CAPTCHA) checks against bots and spam.
* IP address filtering lists to allow or block requests from specified IP addresses.

An ARL profile contains rules for limiting the number of requests to the protected resource based on various conditions.

{% include [sws-type-resources](sws-type-resources.md) %}

{{ sws-name }} allows you to create multiple security profiles and use them to consolidate various security tools.