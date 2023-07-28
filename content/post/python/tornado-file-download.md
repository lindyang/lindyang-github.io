---
title: "Tornado File Download"
date: 2023-07-28T16:33:54+08:00
tags: [python]
---


@plugin.route(r'/question/(\d+)/pdf')
class PartialDocumentHandler(BaseHandler):
    @gen.coroutine
    def get(self, *args, **kwargs):
        question_id, = args
        qry = yield self.db.execute("SELECT data FROM question WHERE id=%s", (question_id,))
        data = qry.fetchone()
        if data:
            path = data[0]['path']
            abspath = os.path.join(project_root, path)
            if os.path.isfile(abspath):
                self.set_header('Content-Type', 'application/octet-stream')
                self.set_header('Content-Disposition', 'attachment; filename=' + urllib.parse.quote(os.path.basename(path)))
                with open(abspath, 'rb') as file:
                    while True:
                        data = file.read(64 * 1024)
                        if not data:
                            break
                        try:
                            self.write(data)
                            yield self.flush()
                        except iostream.StreamClosedError:
                            return
                self.finish()
            else:
                self.not_found('File Not Found')
        else:
            self.not_found('Item Not Found')

