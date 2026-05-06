# Appearance Workflow

## Rules

Appearance preferences can be updated by the signed-in user.

Valid factions are:
1. `jedi`
2. `sith`
3. `rebel_alliance`
4. `galactic_empire`

Default faction is `jedi`.

Each faction has four ranks determined by the number of collected items:
- Rank 1 (0–24 items): Youngling / Acolyte / Trooper / Cadet
- Rank 2 (25–49 items): Padawan / Apprentice / Captain / Commander
- Rank 3 (50–74 items): Knight / Warrior / Major / Admiral
- Rank 4 (75+ items): Master / Darth / General / Grand Moff

The user's current rank is displayed as the account menu button label in the header.

Valid colour modes are:
1. `light`
2. `auto`
3. `dark`

Theme is derived from the selected faction on the client. There is no separate theme field.

Profile updates must upsert the profile row when one does not already exist.

## Client Side Validation

Client side validation occurs when a colour mode, faction, or theme option is selected.

On colour mode selection:
1. If selected colour mode is already active, do nothing
2. If a colour mode save is in progress, ignore additional selections
3. Otherwise call update profile API with selected colour mode

On faction selection:
1. If selected faction is already active, do nothing
2. If an faction save is in progress, ignore additional selections
3. Otherwise call update profile API with selected faction

## Server Side Validation

Server side validation must occur at the API level.

The update profile API must validate:
1. Caller is authenticated
2. Colour mode is one of the valid colour modes when provided
3. Faction is one of the valid factions when provided

If colour mode is invalid, return bad request.

If faction is invalid, return bad request.

## Colour Mode Workflow

1. User opens Profile page
2. User selects Light, Auto, or Dark
3. Client calls update profile API with colour mode
4. API validates JWT
5. API validates colour mode
6. API creates profile row if needed
7. API updates colour mode
8. API returns updated profile
9. Client updates colour mode context
10. Client displays success message

## Faction Workflow

1. User opens Profile page
2. Profile page fetches GET profile API and syncs faction and collected item count to context
3. User selects an faction
4. Client calls update profile API with faction
5. API validates JWT
6. API validates faction
7. API creates profile row if needed
8. API updates faction
9. API returns updated profile
10. Client updates faction context
11. Client displays success message

## Failure Workflow

1. If colour mode save fails, display failed-to-save colour mode message
2. If faction save fails, display failed-to-save faction message
3. If network fails, display network error message
4. Keep the user on the Profile page
