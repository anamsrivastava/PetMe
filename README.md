# PetMe
PetMe is a web platform that connects animals who need help with the people who can give it. The idea is “healthcare-first placement”: every animal, whether adopted, fostered or rescued, is tracked through vaccination, sterilisation and clinic care, not just handed over
It addresses three gaps. Strays have no fast route to help, adoption listings are scattered across social posts, and health records get lost between shelter, foster and home. PetMe puts adoption, healthcare, emergency reporting, donations and volunteering in one place.

**What users can do**
Adopt (adopt.html): browse animal “tag cards”, filter by species and age, search by name or breed, open a profile and send an adoption inquiry. Each pet has a shareable link such as adopt.html#PM-0231.
Donate and rehome (donate.html): give money (UPI, card or net banking, one-time or monthly) or supplies, or submit a pet you can no longer keep.
Healthcare (healthcare.html): vaccination schedule, partner-clinic cards, an interactive map with directions, and a booking form that supports emergency visits.
Emergency SOS (sos-report.html): report an injured or stray animal with urgency, species, location, description and photo.
Volunteer and foster (volunteer.html, foster.html): pick roles such as fostering, transport or intake support, and apply.
Information pages: FAQ, About and founders, Contact, Suggestions.
Accounts (register.html, login.html, profile.html): sign up as adopter, donor, volunteer, foster or pet owner. The profile shows registered pets, donation history and roles.

**How it is built**

Frontend. Plain HTML, CSS and JavaScript with no build step.

css/style.css holds the design system: colour tokens, the kennel-tag card, scroll-reveal animations, a count-up stats strip, and responsive rules.
Each page has its own script (adopt.js, donate.js, sos.js and so on) for filtering, validation and submission.
Shared scripts: main.js (navigation, animations, validation helpers), auth.js, footer.js, api.js, and pets-data.js (the pet records).

**Backend.**
backend/app.py is a Flask app that also serves the frontend, so one command runs everything. It exposes a REST API:

**Endpoint	Purpose**
/api/health	storage and email status
/api/auth/send-otp, /api/auth/verify-otp	emailed login codes
/api/users	registration records
/api/adoptions	adoption inquiries
/api/donations	save a donation (POST) or list donations (GET)
/api/volunteers	volunteer applications
/api/sos	emergency reports
/api/rehome	rehoming intake
/api/contact, /api/newsletter	messages and subscribers
/api/admin/summary	record counts

**Storage. **
If AWS credentials are present, data goes to Amazon DynamoDB tables (petme-users, petme-adoptions and so on). The tables are created automatically on pay-per-request billing. If no credentials are present, everything goes to a local file, backend/data/petme-data.json. The project works either way.

**Deployment.**
Your screenshot shows it running on an AWS EC2 instance, PetMe-EC2, in N. Virginia. Secrets live in backend/.env, never in frontend code.

Two-factor sign-in

**When someone registers or logs in:**

The server generates a fresh random 6-digit code, so it differs every time.
It stores the code in memory for 10 minutes and emails it by SMTP.
The code is deleted after one successful use. There is also a “Resend code” button.
If SMTP is not configured, or the backend is unreachable, the page shows a demo code on screen instead.
Payments

The checkout looks like a real gateway.

UPI: generates a QR code for the chosen amount.
Card: runs a Luhn number check, an expiry check and a CVV check.
Net banking: shows a bank picker.

No money moves. It is a demo flow, and each payment is recorded in the backend and on the donor’s profile.

**Design approach**

The look follows an “intake ledger” idea: kennel-tag pet cards with a punched hole and a status stamp, ledger-style counters, and a “Live SOS” ticker. The latest version also has dark mode and a floating SOS button. It uses animations, with a prefers-reduced-motion fallback.

Social impact and SDGs
SDG 3, Good Health and Well-being: vaccination and sterilisation reduce rabies and zoonotic risk.
SDG 11, Sustainable Cities: fewer abandoned strays and faster response make neighbourhoods safer.
SDG 17, Partnerships: clinics, volunteers, fosters and donors work from one shared record.
SDG 15, Life on Land: supported indirectly, through humane animal welfare.

**What is a prototype**
Accounts and passwords live in the browser’s localStorage, in plain text. A real launch needs server-side accounts with password hashing and sessions.
Payments are simulated.
The login codes are held in server memory, so they are lost if the server restarts. The same applies if you run several server processes.
The healthcare appointment form validates and confirms but is not yet saved to the backend. The adoption, SOS, donation, volunteer, contact and rehoming forms are saved.
Pet photos are loaded from external image links. They should be replaced with images hosted in the project’s own images/pets/ folder.
