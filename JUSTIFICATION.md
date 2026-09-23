Justification — Refactor the Spaghetti Screen

One paragraph per extracted widget: the level it was placed at, and the rule that puts it there.

Atoms

AppHeading. Atom. Rule: "Atoms hold no logic and no state beyond what's needed to render (StatelessWidget only)." It's a StatelessWidget that renders only the text string it's given, nothing else.

AppPrimaryButton. Atom. Same rule — no internal state, no decision-making about what pressing it does. Label, styling, and the onPressed callback are all supplied from outside.

ProductIconAvatar. Atom. Same rule — renders the IconData it's given and nothing more; no knowledge of what a product is.

DeleteIconButton. Atom. Same rule — forwards a press via onPressed but does not itself delete anything; deleting is decided by whoever owns the data.

ProductNameLabel, CategoryLabel, PriceLabel. Atoms. Same rule — each is a StatelessWidget with a fixed style and no state, rendering one piece of text.

SearchTextField. Atom. Same rule — no internal state; every keystroke is forwarded via onChanged.

LabeledTextFormField. Atom. Same rule — validator, controller, and keyboard type are all passed in as parameters, so the atom itself makes no decisions about what counts as valid input.

CategoryDropdownField. Atom. Same rule — value, options, and the change callback are all supplied from outside; it does not decide what a category change means.

CatalogAppBar. Atom. Same rule — title and color are fixed and never change based on state.

Molecules

CatalogSearchBar. Molecule. Rule: molecules are "small groups of atoms functioning as a single unit." It combines AppHeading + SearchTextField into the one unit the user sees as "the search box."

ProductInfo. Molecule. Same rule — combines ProductNameLabel, CategoryLabel, and PriceLabel into the single info block shown on a card.

ProductActions. Molecule. Same rule — combines AppPrimaryButton and DeleteIconButton into the single actions block on a card. Rule also satisfied: "Molecules may hold local UI state ... but never business logic" — it holds none, it only forwards two callbacks.

Organisms

ProductCard. Organism. Rule: organisms are the "complex section[s]" built from molecules and atoms — this combines ProductIconAvatar, ProductInfo, and ProductActions into one full card, and does not own the product list.

ProductCatalogList. Organism. Rule: "Organisms may contain local logic but should not directly own the app's core data." It filters the list it's given by search query, which is local logic, but never stores or mutates that list itself.

AddProductForm. Organism. Same rule. The GlobalKey<FormState>, text controllers, and selected category are local UI state belonging to the form, not the app's core data, so an organism is allowed to hold them. Validation logic lives here too. It does not construct a Product, assign an id, touch _products, or show the confirmation SnackBar — it only reports raw field values upward via onSubmit, keeping the "clear Organism/Page boundary" the rubric asks for.

Template

CatalogPageTemplate. Template. Rule: "Templates accept layout slots as parameters and must never import a data model directly." It takes appBar, searchSection, catalogSection, and formSection as plain Widget slots and never imports Product.

Page

CatalogPage. Page. Rule: "Pages are the only place allowed to hold the actual product list and wire real data downward." It's the only file that declares List<Product> _products, the only place that assigns a new product's id, mutates the list, and shows both SnackBars.

Note on something I was unsure how to classify

The confirmation SnackBar after a successful submit could arguably belong to the form, since it just handled the interaction, or the page, since it owns the data being confirmed. I placed it in the Page, since the message only makes sense once the product has actually been added, and only the Page knows that succeeded.