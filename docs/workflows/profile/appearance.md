# Appearance Workflow

## Rules

Appearance preferences can be updated by the signed-in user.

Valid affiliations are:
1. `jedi`
2. `sith`
3. `rebel_alliance`
4. `galactic_empire`

Default affiliation is `jedi`.

Each affiliation has four ranks determined by the number of collected items:
- Rank 1 (0–24 items): Youngling / Acolyte / Trooper / Cadet
- Rank 2 (25–49 items): Padawan / Apprentice / Captain / Commander
- Rank 3 (50–74 items): Knight / Warrior / Major / Admiral
- Rank 4 (75+ items): Master / Darth / General / Grand Moff

The user's current rank is displayed as the account menu button label in the header.

Valid colour modes are:
1. `light`
2. `auto`
3. `dark`

Valid themes are:
1. `default`
2. `mustafar`
3. `endor`
4. `hoth`
5. `dagobah`
6. `coruscant`
7. `tatooine`

Legacy saved value `imperial` is accepted for compatibility and displayed as `mustafar`.

Legacy saved value `rebel` is accepted for compatibility and displayed as `endor`.

Legacy saved value `jedi` is accepted for compatibility and displayed as `hoth`.

Legacy saved value `crawl` is accepted for compatibility and displayed as `dagobah`.

Profile updates must upsert the profile row when one does not already exist.

## Client Side Validation

Client side validation occurs when a colour mode, affiliation, or theme option is selected.

On colour mode selection:
1. If selected colour mode is already active, do nothing
2. If a colour mode save is in progress, ignore additional selections
3. Otherwise call update profile API with selected colour mode

On affiliation selection:
1. If selected affiliation is already active, do nothing
2. If an affiliation save is in progress, ignore additional selections
3. Otherwise call update profile API with selected affiliation

On theme selection:
1. If selected theme is already active, do nothing
2. If a theme save is in progress, ignore additional selections
3. Otherwise call update profile API with selected theme

## Server Side Validation

Server side validation must occur at the API level.

The update profile API must validate:
1. Caller is authenticated
2. Colour mode is one of the valid colour modes when provided
3. Theme is one of the valid themes when provided
4. Affiliation is one of the valid affiliations when provided

If colour mode is invalid, return bad request.

If theme is invalid, return bad request.

If affiliation is invalid, return bad request.

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

## Theme Workflow

1. User opens Profile page
2. User selects a theme
3. Client calls update profile API with theme
4. API validates JWT
5. API validates theme
6. API creates profile row if needed
7. API updates theme
8. API returns updated profile
9. Client updates theme context
10. Client displays success message

## Affiliation Workflow

1. User opens Profile page
2. Profile page fetches GET profile API and syncs affiliation and collected item count to context
3. User selects an affiliation
4. Client calls update profile API with affiliation
5. API validates JWT
6. API validates affiliation
7. API creates profile row if needed
8. API updates affiliation
9. API returns updated profile
10. Client updates affiliation context
11. Client displays success message

## Failure Workflow

1. If colour mode save fails, display failed-to-save colour mode message
2. If affiliation save fails, display failed-to-save affiliation message
3. If theme save fails, display failed-to-save theme preference message
4. If network fails, display network error message
5. Keep the user on the Profile page
