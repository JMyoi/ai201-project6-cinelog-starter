

Adds a watchlist feature so users can save films they want to watch. Includes a new WatchlistEntry model, service functions, and REST endpoints.


I notice watchlists default to public=True. We don't have a documented decision on default visibility for user lists. Before I can approve this, I need you to add a note to your PR description explaining your reasoning. I want to make sure we're being intentional here, not just inheriting a default.


Please add a test for the case where film_id doesn't exist in the database. Look at the existing tests in test_collection.py — the pattern is there.


services/watchlist_service.py
from services.collection_service import FilmNotFoundError


def save_to_watchlist(user_id, film_id):
@jamjamgobambam
jamjamgobambam
on Mar 16
Owner
Author
save_to_watchlist() should follow the project's naming convention. Compare with add_to_collection() — the pattern here is verb_to_noun. Please rename to add_to_watchlist() and update all call sites.



services/watchlist_service.py
    if film is None:
        raise FilmNotFoundError(f"No film found with id '{film_id}'")

    entry = WatchlistEntry(user_id=user_id, film_id=film_id)
@jamjamgobambam
jamjamgobambam
on Mar 16
Owner
Author
What happens if a user calls this with a film that's already on their watchlist? The current implementation would add a duplicate entry. Please handle this case.



services/watchlist_service.py
        WatchlistEntry.query
        .filter_by(user_id=user_id)
        .join(Film)
        .order_by(Film.title.asc())
@jamjamgobambam
jamjamgobambam
on Mar 16
Owner
Author
I'd prefer watchlists to default to "date added" order rather than alphabetical. Most users want to see what they added recently. I'm open to discussion if you see it differently — but let's make a decision and document it.


A refactor merged to main that changed film IDs from integers to UUIDs. Your watchlist code still references integer IDs. Please rebase on main and update accordingly.