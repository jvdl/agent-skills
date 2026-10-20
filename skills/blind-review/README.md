# Blind Review skill

This skill allows users to request a review of their code. It provides feedback on code quality, readability, and potential issues without making any changes to the code itself.

This skill will spawn a separate agent that does not have knowledge in its session about how the code was written and what decisions were made, only the claims and the code.

If you pass along instructions for where to test and any credentials to use, then the skill will spawn a review against those. For example ``/blind-review frontend is running at http://localhost:5173, use `.env.local` to read the admin credentials from LOCAL_E2E_ADMIN_* variables``. This works even better if you get your agent to store those details in memory so you can invoke it like `/blind-review test the UI with admin credentials`
