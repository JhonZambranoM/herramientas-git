---
Para guardar con relacion
public Libros guardarLibrosConPublicacion(Libros libros, Long id_publicacion){
        //recuperar lapublicacnion por id;
        Optional<Publicacion> optionalPublicacion = publicacionesLibros.findById(id_publicacion);

        //verificamos si la publicacion existe
        if (optionalPublicacion.isPresent()){
            //establecer la relacion en ambos lados(Libros como en publicaicones)
            Publicacion publicacion = optionalPublicacion.get();
            libros.setPublicacions(publicacion);
            publicacion.getLibros().add(libros);
            return librosRepo.save(libros);
        }else{
            return null;
        }
    }
	
	   @PostMapping("/libros/{id_publicacion}")
    public Libros guardarConPublicaciones(@RequestBody Libros libros,
                                          @PathVariable Long id_publicacion){
        return libroServicio.guardarLibrosConPublicacion(libros,id_publicacion);
    }


---generarpdf
 @GetMapping("/generar-pdf")
    public ResponseEntity<byte[]> generarInformePDF() {
        List<Libros> libros = libroServicio.listarTodosLibros();

        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        try {
            Document document = new Document();
            PdfWriter.getInstance(document, baos);
            document.open();

            PdfPTable table = new PdfPTable(6); // 6 columnas para las propiedades de Libros
            addTableHeader(table);
            addRows(table, libros);

            document.add(table);
            document.close();
        } catch (Exception e) {
            e.printStackTrace();
        }

        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_PDF);
        headers.setContentDispositionFormData("inline", "informe.pdf");

        return new ResponseEntity<>(baos.toByteArray(), headers, HttpStatus.OK);
    }

    private void addTableHeader(PdfPTable table) {
        table.addCell("Título");
        table.addCell("Números de Páginas");
        table.addCell("ISBN");
        table.addCell("Fecha Creación");
        table.addCell("Fecha Modificación");
        table.addCell("Año de Publicación");
    }

    private void addRows(PdfPTable table, List<Libros> libros) {
        for (Libros libro : libros) {
            table.addCell(libro.getTitulo());
            table.addCell(String.valueOf(libro.getNum_pages()));
            table.addCell(libro.getIsbn());
            table.addCell(libro.getFecha_creacion().toString());
            table.addCell(libro.getFecha_modificacion().toString());
            table.addCell(String.valueOf(libro.getAnio_pub()));
        }
    }
  <dependency>
            <groupId>com.github.librepdf</groupId>
            <artifactId>openpdf</artifactId>
            <version>1.3.33</version>
        </dependency>
