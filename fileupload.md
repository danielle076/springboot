## File Upload

Als je een foto of een document hebt dan wordt dit gezien als een binary iets. 

Je kunt twee kanten op: je slaat het op in een database. In IntelliJ doe je het volgende.

    @Lob
    private Blob data;

Op deze manier slaat hij de foto op in je database. In de database wordt het een nieuwe column.

De andere manier is dat je het kunt opslaan in een directory. Je maakt een nieuw directory `uploads`. Via de controller en service komt het bestand in de uploads.



