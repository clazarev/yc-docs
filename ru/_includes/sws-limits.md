#### Квоты {#quotas}

#|
|| Вид ограничения | Значение ||
|| Максимальное количество [профилей безопасности](../smartwebsecurity/concepts/profiles.md) в [облаке](../resource-manager/concepts/resources-hierarchy.md#cloud)
`smart-web-security.securityProfiles.count` | 5 ||
|| Максимальное количество [профилей ARL](../smartwebsecurity/concepts/arl.md) в облаке
`smart-web-security.advancedRateLimiterProfiles.count` | 5 ||
|| Максимальное количество [профилей WAF](../smartwebsecurity/concepts/waf.md) в облаке
`smart-web-security.wafProfiles.count` | 10 ||
|| Максимальное суммарное количество [правил профилей безопасности](../smartwebsecurity/concepts/rules.md) в облаке
`smart-web-security.securityProfileRules.count` | 250 ||
|| Максимальное суммарное количество [правил-исключений WAF](../smartwebsecurity/concepts/waf.md#exclusion-rules) в облаке
`smart-web-security.wafProfileExclusionRules.count` | 250 ||
|| Максимальное суммарное количество [правил в профилях ARL](../smartwebsecurity/concepts/rules.md#arl-rules) в облаке
`smart-web-security.advancedRateLimiterProfileRules.count` | 250 ||
|| Максимальное суммарное количество [условий](../smartwebsecurity/concepts/conditions.md) для сопоставления IP-адресов в облаке
`smart-web-security.ipMatchers.count` | 2 500 ||
|| Максимальное суммарное количество [списков IP-адресов](../smartwebsecurity/concepts/lists.md) в {{ sws-name }}
`smart-web-security.matchLists.count` | 20 ||
|| Максимальное суммарное количество [прокси-серверов](../smartwebsecurity/concepts/domain-protect.md#proxy) в облаке 
`smart-web-security.loadBalancers.count` | 1 ||
|| Максимальное суммарное количество [доменов](../smartwebsecurity/concepts/domain-protect.md#domain) в облаке 
`smart-web-security.loadBalancerDomains.count` | 10 ||
|#

#### Лимиты {#limits}

**Вид ограничения** | **Значение**
----- | -----
Максимальное количество условий `HTTP header` в одном правиле | 20
Максимальное количество условий `Query Match` в одном правиле | 20
Максимальное количество IP-адресов, префиксов или диапазонов адресов в одном условии | 10 000
Максимальное количество условий `HTTP method` в одном правиле | 20
Максимальное количество условий `Host` в одном правиле | 20
Максимальная длина строки регулярного выражения | 255 символов
Максимальное количество запросов в секунду (RPS) суммарно на все [виртуальные хосты](../application-load-balancer/concepts/http-router.md#virtual-host)<br/> балансировщика, подключенные к одному профилю безопасности^1^ | 20 000
Максимальное количество доменов для одного прокси-сервера | 20
Максимальное количество IP-адресов целевых ресурсов для одного домена | 20

^1^ Если вы ожидаете большее количество запросов в секунду, обратитесь к вашему аккаунт-менеджеру {{ yandex-cloud }}.