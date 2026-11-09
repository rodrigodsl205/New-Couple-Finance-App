# Project memory

## Product intent & audience

- Personal/group finance manager (receitas, despesas, contas, cartões, faturas, acertos, categorias, orçamentos, metas)
- Supports personal spaces and shared spaces via email invitations
- Audience: individual users and small groups managing shared finances
- Language: Portuguese (pt-BR) for UI/content

## Brand personality & voice

- Professional yet approachable; clarity over cleverness
- Concise, status-driven copy (convite pendente/aceito/revogado/expirado, empty states)

## Visual design & UX direction

- Design system implemented in Tailwind: deep green primary, Inter typography, 8px spacing scale
- Reusable component classes: btn, input, card, badge
- Responsive layout: sidebar on desktop, bottom navigation on mobile
- Empty states designed to be elegant, not bare
- Icons via lucide-react; charts via recharts
- Animations included in design system
- Category selection via modal in selection mode: click "Selecione" button → modal opens → click category to select (no gear/filter icon)
- Categories separated by type: expense categories only in expense form, revenue categories only in revenue form

## Decisions, constraints & rationale

- Stack: Vite + React (JSX), Tailwind, lucide-react, react-router-dom, recharts, react-hook-form, zod, date-fns, vite-plugin-pwa
- Path alias `@/` → `src/` (do not use deep relative imports)
- Backend: Supabase (auth, DB with RLS, Realtime)
- DB tables: `profiles`, `financial_spaces`, `space_members`, `invitations`
- RLS enabled on all tables with ownership/association policies
- Auto triggers: signup creates profile + personal space automatically
- Default payment methods pre-seeded on new space creation
- Invitation tokens: high-entropy UUID, 7-day expiry, single-use
- Auth flow: login, signup, password recovery, password reset; persistent session via `onAuthStateChange`
- Realtime sync active for spaces, members, invitations
- Credit card fields: nome, bandeira, últimos 4 dígitos, titular, limite total, dia fechamento, dia vencimento
- Invoice fields: cartão vinculado, período, fechamento, vencimento, forma de pagamento; supports purchases (à vista/parceladas) and partial/full payments
- Invoice status auto-calculated: em aberto, fechada, parcial, paga
- Expense form fields: estabelecimento, CPF/CNPJ (opcional), forma de pagamento
- Card field removed from base expense form; only shown when payment method starts with "Cartão"
- When card payment selected: due date field replaced by card selector + open invoice selector for that card
- Transactions table has `card_id` and `invoice_item_id` but no `invoice_id`; invoice selection in form is informational (no direct DB link yet)
- `useInvoices` hook integrated into TransactionsPage for fetching open invoices by card
- CategoryManageModal supports selection mode with kind filtering (receita/despesa)
- Build verified passing after category/card/invoice form changes

## Open questions & assumptions

- PWA plugin config in `vite.config.ts` was in progress — needs completion
- Phase 7 (orçamentos, metas, relatórios) not started — proposed as next step
- Placeholder pages likely remaining for: acertos, orçamentos, metas (confirm before starting)
- Invoice selection on expense form is informational only — no `invoice_id` column on transactions table; may need schema migration or invoice_item auto-creation to properly link expenses to invoices
- When no open invoices exist for a selected card, user is directed to Faturas tab — consider auto-creating open invoices