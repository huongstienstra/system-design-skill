# Mobile and web clients

For a web client, skip the OS-version, store-review, and app-size items.

## Questions
Ranked; re-rank for the prompt. The default follows each one.

1. Must it work offline? Default: read offline from cache; writes need the network.
2. Platforms? Default: iOS and Android.
3. Login needed? Default: yes, with a token refresh.
4. How fresh must data be: realtime, on open, or pull-to-refresh? Default: refresh on open, plus pull-to-refresh.
5. What makes two lists the same cache entry, and how long is an entry fresh? Default: one entry per query; fresh for 30 minutes.
6. Which third-party APIs does it call, and what are their quotas and keys? Default: no direct third-party calls; go through our backend.
7. Analytics and privacy rules? Default: no raw user input or personal data in events.
8. Minimum OS version, and how long must old app versions keep working? Default: the last 4 OS versions; old app versions supported for 6 months.
9. Push notifications? Default: none.

## Scale dimensions
- Payload per screen: items × bytes per item, with images counted separately.
- Images: count × size per screen; they usually dominate data use.
- On-device storage: cached items × item size, plus the eviction rule that bounds it.
- Requests per user per day, and that number × users against each API quota.
- Cold start: reads and work done before the first frame.
- Battery: background work per day.
- App size when the client cares about it.

## Usual failures
- No network or a flaky network: a request hangs, or fails halfway through a paginated list.
- The cache is stale or empty when the network fails.
- A refresh that replaces the cache erases user state stored beside it (favorites, drafts, read marks).
- The process is killed in the middle of a flow; state that only lived in memory is lost.
- A token expires while offline, or during a burst of parallel requests.
- An old app version calls an API that changed.
- An API key shipped inside the app gets extracted and abused, or its quota is shared by every install.
- Offline writes conflict with the server when the device reconnects.
- A large list or image grid uses too much memory or janks while scrolling.
- An analytics event leaks user input, a URL, or an identifier.
