# GraphQL PokéAPI

This is a federated GraphQL API using data from the PokéAPI SQLite3 database. It is built using Prisma Apollo Federation, and TypeScript.

## Getting the database

Currently, updating the data in the `db.sqlite3` file is a manual process. Follow the docs in the [PokéAPI README](https://github.com/PokeAPI/pokeapi). This is also the same process I use to update the database file for this server at the moment. This will eventually be automated. 

Here are the steps from that README that I took:

1. Clone the PokéAPI repo

```bash
git clone https://github.com/PokeAPI/pokeapi.git
```

2. Update the `Makefile` -- I needed to update all of the `pip` and `python` commands to `pip3` and `python3`.

3. In the root of cloned PokéAPI repo, install the requirements:

```bash
make install
```

4. Set up the local dev environment:

```bash
make setup
```

5. Start the Django shell:

```bash
python3 manage.py shell --settings=config.local
```

6. Run the build script:

```bash
from data.v2.build import build_all
build_all()
```

7. The build script will build a LOT of tables, so it might take a minute. Once it's done, you'll see a `db.sqlite3` file in the root of your PokéAPI repo.

8. The boolean `bool` data types in the `.sqlite3` file generated by PokéAPI is not compatible with Prisma, so those columns will need to be updated to be `BOOLEAN` data types.

## Updating the database file

For the SQL queries and manual database table updates, I use [SQLiteStudio](https://sqlitestudio.pl/), a free desktop application for browsing and editing SQLite database files.

1. Open a new database by clicking on the "Add a database" button (you might have to hover over the icons to see the labels).
2. Make sure the "Database Type" is set to `SQLite 3`.
3. For the "File" input, click on the folder icon to browse your computer for an existing database file. Navigate to the `db.sqlite3` file that you're using for your server.
4. Optionally give the database a "name" input. This will just be the name that it's under in the SQLiteStudio sidebar.
5. Click "OK"

You can explore the tables by clicking on the drop down arrows for the database you just opened in SQLiteStudio. To make a query, click on the "Open SQL Editor" button. 

### Changing column data types

1. In SQLiteStudio, open up a table by double clicking on it. 

2. Navigate to the cell for the column's data type that you'd like to change. Double click on it to open up the modal for that column. 

3. change the data type from the input dropdown. Click OK when you're done.

4. Once you're done making changes to the table, click on the green check icon ✅ to commit the structural changes. 

## Generating Prisma models

For initializing the Prisma models based off of the SQLite3 tables, run the `prisma db pull` script or use the local script from this repo's `package.json`:

```bash
npm run init-models
```

> If you missed updating any of the incompatible data types, Prisma will output a list telling you which model, the name of the field, and it's original data type from the `.sqlite3` file that it doesn't support. From there, you can go back into your tables in SQLiteStudio and make any necessary changes. Then make sure to update your Prisma models to reflect the database changes.

## Roadmap/To Do

- [ ] Prisma schema improvements to be better API rather than just direct reflection of db table structure
- [ ] Create GraphQL schema, subgraphs
- [ ] Create resolvers
- [ ] Federate subgraphs w/ router
- [ ] Automate process of update db --> generate updated Prisma models --> update Prisma models to reflect API we actually want
- [ ] Write tests