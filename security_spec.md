# Security Specification - Glidrovia

## Data Invariants
1. A **User** document must match the authenticated user's UID.
2. **Games** must have a `creatorUid` matching the author.
3. **Videos** must have a `creatorUid` matching the author.
4. **Studio Maps** are keyed by username and should only be writable by the user who owns that username.
5. **Likes** on videos or games should be atomic or properly tracked (though currently, it looks like simple arrays or fields).
6. **Global Settings** should only be writable by administrators.

## The "Dirty Dozen" Payloads (Examples)

1. **Identity Spoofing (User)**: Authenticated user A trying to create/update `users/userB`.
2. **Privilege Escalation (User)**: User trying to set `role: 'admin'` on their own profile.
3. **State Shortcutting (Game)**: Trying to update a game once it's in a terminal state (if any).
4. **Shadow Field Injection**: Adding `isVerified: true` to a User document.
5. **Orphaned Write (Game)**: Creating a game with a `creatorUid` that doesn't exist (though difficult with auth).
6. **Resource Exhaustion**: Sending a 1MB string for `username`.
7. **Cross-User Map Edit**: User A trying to save to `studio_maps/userB`.
8. **Invalid Liked List**: Pushing a non-UID string into a `likes` array.
9. **Timestamp Spoofing**: Sending a client-side `createdAt` date instead of server time.
10. **Global Setting Hijack**: Non-admin trying to update `global_settings/main`.
11. **Username Takeover**: User trying to create `/users_by_username/admin` without owning it.
12. **Video Deletion**: User A trying to delete User B's video.

## Test Runner (Draft)
A `firestore.rules.test.ts` would normally verify these. I will proceed to draft hardened rules.
