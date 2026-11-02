# Meta (Facebook) ASN and IP Lists

The networks operated by Meta (Facebook, Instagram, WhatsApp). Use them to check that a request claiming to be Meta's crawler (`facebookexternalhit`, `meta-externalagent`) really comes from Meta, since faked User-Agents are common.

## Files

| File | Contents | Raw URL |
|---|---|---|
| `facebook_asn_list.lst` | Meta (Facebook)'s ASNs (`AS12345` format) | https://raw.githubusercontent.com/SecOps-Institute/FacebookIPLists/master/facebook_asn_list.lst |
| `facebook_ipv4_cidr_blocks.lst` | **IPv4 ranges merged into the fewest non-overlapping CIDR blocks. Use this one for firewalls and allowlists.** | https://raw.githubusercontent.com/SecOps-Institute/FacebookIPLists/master/facebook_ipv4_cidr_blocks.lst |
| `facebook_ip_list.lst` | IPv4 routes as registered (these can overlap) | https://raw.githubusercontent.com/SecOps-Institute/FacebookIPLists/master/facebook_ip_list.lst |
| `facebook_ipv6_list.lst` | IPv6 routes as registered | https://raw.githubusercontent.com/SecOps-Institute/FacebookIPLists/master/facebook_ipv6_list.lst |
| `Last-Checked-At` | Time of the last successful check (IST) | |

Each file has one entry per line. Entries are normalized, deduplicated and sorted numerically,
with no comments.

## How it is built

Meta (Facebook) doesn't publish an official IP list, so this one is derived from public internet registry
data:

1. **ASNs:** search [RIPEstat](https://stat.ripe.net/) for Autonomous Systems whose registered
   name contains "Facebook". An ASN is kept only if the registry description really names the company.
2. **Routes:** for each ASN, get its `route` / `route6` objects from the
   [RADb](https://www.radb.net/) routing registry.
3. **Remove stale routes:** keep a route only if it falls inside a prefix that the company's ASNs
   have announced in BGP in the last ~14 days (RIPEstat announced-prefixes). Registries keep many
   old records, and this drops them.
4. **Merge:** combine the IPv4 routes into the CIDR blocks file.

## Update schedule

**mirabot**, an automated bot, checks **every hour**:
- `Last-Checked-At` is updated on every successful check.
- The list files change only when the company's registered or announced networks change, so this
  repo's commit history doubles as a change log.

Before publishing, the bot checks each update for problems: minimum size, malformed data, and
any sudden drop of more than half the entries. If a check fails, the previous list stays in
place. If `Last-Checked-At` is more than a few hours old, an update is being held back or the
bot is down.

## Notes

- **Non-exhaustive.** Networks the company never registers in RADb, or announces from ASNs not
  named after it, won't appear.
- Meta's own [crawler documentation](https://developers.facebook.com/docs/sharing/webmasters/web-crawlers) recommends this same method: look up the routes of Meta's ASN in RADb.
- `AS63293` (FACEBOOK-OFFNET) covers Meta's edge caches. Some of these sit inside ISPs' networks.

## License

MIT for this repository. Derived from public RIPEstat and RADb data.

---
Updated hourly by **mirabot**, sponsored by [endlesstokens.ai](https://endlesstokens.ai) — *Pay for Time not Tokens*.

Maintained by [SecOps Institute](https://github.com/SecOps-Institute) ·
[LinkedIn](https://www.linkedin.com/in/aarvee11) · [Blog](https://amisafe.secops.in)
